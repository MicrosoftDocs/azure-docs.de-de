---
title: SAP-Workloadkonfigurationen mit Azure-Verfügbarkeitszonen | Microsoft-Dokumentation
description: Architektur und Szenarien für Hochverfügbarkeit für SAP NetWeaver mit Azure-Verfügbarkeitszonen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad9820cc3227c788582a9c2a452abe1886a4a2d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019326"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP-Workloadkonfigurationen mit Azure-Verfügbarkeitszonen
[Azure-Verfügbarkeitszonen](../../../availability-zones/az-overview.md) sind eines der Hochverfügbarkeitsfeatures von Azure. Die Verwendung von Verfügbarkeitszonen verbessert die allgemeine Verfügbarkeit von SAP-Workloads in Azure. Dieses Feature steht bereits in einigen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/) zur Verfügung. Künftig wird es in weiteren Regionen verfügbar sein.

Die folgende Grafik zeigt die grundlegende Architektur von SAP-Hochverfügbarkeit:

![Standardkonfiguration für Hochverfügbarkeit](./media/sap-ha-availability-zones/standard-ha-config.png)

Die SAP-Anwendungsschicht wird in einer [Verfügbarkeitsgruppe](../../manage-availability.md) von Azure bereitgestellt. Für die Hochverfügbarkeit von SAP Central Services können Sie zwei virtuelle Computer in einer separaten Verfügbarkeitsgruppe bereitstellen. Verwenden Sie bei einem Infrastruktur- oder Softwareproblem Windows Server-Failoverclustering oder Pacemaker (Linux) als Hochverfügbarkeits-Framework mit automatischem Failover. Weitere Informationen zu diesen Bereitstellungen finden Sie unter den folgenden Links:

- [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster per Dateifreigabe in Azure](./sap-high-availability-guide-wsfc-file-share.md)
- [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server für SAP-Anwendungen](./high-availability-guide-suse.md)
- [Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver unter Red Hat Enterprise Linux](./high-availability-guide-rhel.md)

Eine ähnliche Architektur wird für die DBMS-Schicht von SAP NetWeaver, S/4HANA oder Hybris-Systemen angewandt. Zum Schutz vor einem Infrastruktur- oder Softwareausfall stellen Sie die DBMS-Ebene in einem Aktiv/Passiv-Modus mit einer Failovercluster-Lösung bereit. Die Failoverclusterlösung kann ein DBMS-spezifisches Failoverframework, Windows Server-Failoverclustering oder Pacemaker sein.

Zum Bereitstellen derselben Architektur mit Azure-Verfügbarkeitszonen müssen einige Änderungen an der oben beschriebenen Architektur vorgenommen werden. Dieser Artikel beschreibt diese Änderungen.

## <a name="considerations-for-deploying-across-availability-zones"></a>Überlegungen zur Bereitstellung über Verfügbarkeitszonen hinweg


Beachten Sie Folgendes, wenn Sie Verfügbarkeitszonen verwenden:

- Es gibt keine Garantien in Bezug auf die Abstände zwischen verschiedenen Verfügbarkeitszonen innerhalb einer Azure-Region.
- Verfügbarkeitszonen sind keine ideale Lösung zur Notfallwiederherstellung. Naturkatastrophen können in ganzen Regionen weitreichende Schäden verursachen, einschließlich schwerer Schäden an der Energieinfrastruktur. Die Abstände zwischen verschiedenen Zonen sind möglicherweise nicht ausreichend für eine ordnungsgemäße Lösung für die Notfallwiederherstellung.
- Die Netzwerklatenz über Verfügbarkeitszonen hinweg ist nicht für alle Azure-Regionen gleich. In einigen Fällen können Sie die SAP-Anwendungsschicht in verschiedenen Zonen bereitstellen und ausführen, da die Netzwerklatenz von einer Zone zur aktiven DBMS-VM akzeptabel ist. In einigen Azure-Regionen kann die Latenzzeit jedoch zwischen der aktiven DBMS-VM und der SAP-Anwendungsinstanz bei Bereitstellung in verschiedenen Zonen für SAP-Geschäftsprozesse nicht akzeptabel sein. In diesen Fällen müssen sich die Bereitstellungsarchitekturen für eine Aktiv/Aktiv-Architektur für die Anwendung und eine Aktiv/Passiv-Architektur mit zu hoher zonenübergreifende Latenz unterscheiden.
- Treffen Sie die Entscheidung, wo Sie Verfügbarkeitszonen verwenden, auf der Grundlage der Netzwerklatenz zwischen den Zonen. Die Netzwerklatenz spielt in zwei Bereichen eine wichtige Rolle:
    - Bei der Latenz zwischen den beiden DBMS-Instanzen, bei denen eine synchrone Replikation erforderlich ist. Je höher die Netzwerklatenz, desto höher die Wahrscheinlichkeit einer Auswirkung auf die Skalierbarkeit Ihrer Workload.
    - Bei dem Unterschied in der Netzwerklatenz zwischen einem virtuellen Computer, der eine SAP-Dialoginstanz in derselben Zone wie die aktive DBMS-Instanz ausführt, und einer ähnlichen VM in einer anderen Zone. Wenn dieser Unterschied wächst, steigen auch die Auswirkungen auf die Ausführungszeit von Geschäftsprozessen und Batchaufträgen, abhängig davon, ob sie in der gleichen Zone wie der DBMS oder in einer anderen Zone ausgeführt werden.

Bei der Bereitstellung von Azure-VMs über Verfügbarkeitszonen hinweg und der Einrichtung von Failoverlösungen innerhalb derselben Azure-Region gelten einige Einschränkungen:

- Sie müssen [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) verwenden, wenn Sie eine Bereitstellung in Azure-Verfügbarkeitszonen durchführen. 
- Die Zuordnung der Zonenaufzählungen zu den physischen Zonen liegt auf einer Azure-Abonnementbasis fest. Wenn Sie für die Bereitstellung Ihrer SAP-Systeme verschiedene Abonnements verwenden, müssen Sie die idealen Zonen für jedes Abonnement definieren.
- Sie können Azure-Verfügbarkeitsgruppen nicht in einer Azure-Verfügbarkeitszone bereitstellen, solange Sie keine [Azure-Näherungsplatzierungsgruppe](../../linux/co-location.md) verwenden. Wie Sie die SAP DBMS-Schicht und die zentralen Dienste zonenübergreifend bereitstellen, gleichzeitig die SAP-Anwendungsschicht mit Hilfe von Verfügbarkeitsgruppen bereitstellen und dennoch große Nähe der VMs erreichen können, ist im Artikel [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](sap-proximity-placement-scenarios.md) dokumentiert. Wenn Sie die Azure-Näherungsplatzierungsgruppen nicht nutzen, müssen Sie die eine oder andere als Bereitstellungsframework für VMs auswählen.
- Sie können den [Azure Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) nicht zum Erstellen von Failoverclusterlösungen auf der Grundlage von Windows Server-Failoverclustering oder Pacemaker unter Linux verwenden. Stattdessen müssen Sie die [Azure-SKU Load Balancer Standard](../../../load-balancer/load-balancer-standard-availability-zones.md) verwenden.



## <a name="the-ideal-availability-zones-combination"></a>Die ideale Kombination von Verfügbarkeitszonen
Bevor Sie entscheiden, wie Sie Verfügbarkeitszonen verwenden, müssen Sie Folgendes ermitteln:

- Die Netzwerklatenz in den drei Zonen einer Azure-Region. Hierdurch können Sie die Zonen auswählen, bei denen im zonenübergreifenden Netzwerkdatenverkehr die geringste Netzwerklatenz auftritt.
- Den Unterschied zwischen der VM-zu-VM-Latenz innerhalb einer der von Ihnen ausgewählten Zonen und der Netzwerklatenz zwischen den beiden ausgewählten Zonen.
- Die Festlegung, ob die VM-Typen, die bereitgestellt werden müssen, in den beiden ausgewählten Zonen verfügbar sind. Bei einigen virtuellen Computern, vor allem VMs der M-Serie, können in bestimmten Situationen einige SKUs in nur zwei der drei Zonen verfügbar sein.

## <a name="network-latency-between-and-within-zones"></a>Netzwerklatenz zwischen und innerhalb von Zonen
Um die Latenz zwischen den verschiedenen Zonen zu ermitteln, müssen Sie Folgendes erledigen:

- Stellen Sie die VM-SKU bereit, die Sie für Ihre DBMS-Instanz in allen drei Zonen verwenden möchten. Stellen Sie sicher, dass [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) (Beschleunigter Azure-Netzwerkbetrieb) bei der Durchführung dieser Messung aktiviert ist.
- Wenn Sie die beiden Zonen mit der geringsten Netzwerklatenz ermittelt haben, stellen Sie drei weitere virtuelle Computer der VM-SKU bereit, die Sie als Anwendungsschicht-VM in den drei Verfügbarkeitszonen verwenden möchten. Messen Sie die Netzwerklatenz für die beiden DBMS-VMs in den beiden anderen DBMS-Zonen, die Sie ausgewählt haben. 
- Verwenden Sie als Messtool **NIPING**. Dieses Tool von SAP wird in den SAP-Supporthinweisen [500235](https://launchpad.support.sap.com/#/notes/500235) und [1100926](https://launchpad.support.sap.com/#/notes/1100926/E) beschrieben. Konzentrieren Sie sich auf die Befehle, die für Latenzmessungen dokumentiert sind. Da **ping** nicht über die Codepfade des beschleunigten Azure-Netzwerkbetriebs funktioniert, wird von der Verwendung abgeraten.

Diese Tests müssen nicht manuell durchgeführt werden. Eine PowerShell-Prozedur [Test der Latenz der Verfügbarkeitszonen](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) ist verfügbar, die die beschriebenen Latenztests automatisiert. 

Basierend auf Ihren Messungen und der Verfügbarkeit Ihrer VM-SKUs in den Verfügbarkeitszonen müssen Sie einige Entscheidungen treffen:

- Definieren der idealen Zonen für die DBMS-Ebene.
- Festlegen, ob Sie Ihre aktive SAP-Anwendungsschicht basierend auf Unterschieden in der Netzwerklatenz in einer Zone und zonenübergreifend auf eine, zwei oder alle drei Zonen verteilen möchten.
- Festlegen, ob Sie aus der Sicht einer Anwendung eine Aktiv/Passiv- oder Aktiv/Aktiv-Konfiguration bereitstellen möchten. (Diese Konfigurationen werden weiter unten in diesem Artikel erläutert.)

Berücksichtigen Sie bei diesen Entscheidungen auch die SAP-Empfehlungen zur Netzwerklatenz im SAP-Hinweis [1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Ihre Messungen und Entscheidungen gelten für das Azure-Abonnement, mit dem Sie diese Messungen vorgenommen haben. Wenn Sie ein anderes Azure-Abonnement verwenden, müssen Sie die Messungen wiederholen. Die Zuordnung der aufgelisteten Zonen kann sich für ein anderes Azure-Abonnement unterscheiden.


> [!IMPORTANT]
> Es ist anzunehmen, dass die Ergebnisse der oben durchgeführten Messungen in jeder Azure-Region, die [Verfügbarkeitszonen](../../../availability-zones/az-overview.md) unterstützt, unterschiedlich ausfallen. Auch wenn sich Ihre Anforderungen an die Netzwerklatenz nicht ändern, müssen Sie möglicherweise in verschiedenen Azure-Regionen unterschiedliche Bereitstellungsstrategien einsetzen, da die Netzwerklatenz zwischen Zonen unterschiedlich sein kann. In einigen Azure-Regionen kann die Netzwerklatenz zwischen den drei verschiedenen Zonen stark unterschiedlich ausfallen. In anderen Regionen kann die Netzwerklatenz zwischen den drei verschiedenen Zonen einheitlicher sein. Die Aussage, dass immer eine Netzwerklatenz zwischen 1 und 2 Millisekunden vorliegt, ist nicht richtig. Die Netzwerklatenz über Verfügbarkeitszonen hinweg in Azure-Regionen kann nicht verallgemeinert werden.

## <a name="activeactive-deployment"></a>Aktiv/Aktiv-Bereitstellung
Diese Bereitstellungsarchitektur wird als „Aktiv/Aktiv“ bezeichnet, da Sie Ihre aktiven SAP-Anwendungsserver über zwei oder drei Zonen bereitstellen. Die SAP Central Services-Instanz, die die Replikation in die Warteschlange einreiht, wird zwischen zwei Zonen bereitgestellt. Dies gilt auch für die DBMS-Ebene, die über dieselben Zonen wie SAP Central Services bereitgestellt wird.

Wenn Sie eine Konfiguration dieser Art in Erwägung ziehen, müssen Sie die beiden Verfügbarkeitszonen in Ihrer Region ermitteln, die eine zonenübergreifende Netzwerklatenz bieten, die für Ihre Workload und Ihre synchrone DBMS-Replikation akzeptabel ist. Sie sollten außerdem sicherstellen, dass das Delta zwischen der Netzwerklatenz in den ausgewählten Zonen und der zonenübergreifenden Netzwerklatenz nicht zu groß ist. Der Grund dafür besteht darin, dass in Abhängigkeit davon, ob ein Auftrag in der Zone mit dem DBMS-Server oder zonenübergreifend ausgeführt wird, große Laufzeitvariationen für Ihre Geschäftsprozesse und Batchprozesse vermieden werden sollten. Einige Variationen sind zulässig, aber nicht Unterschiedsfaktoren.

Ein vereinfachtes Schema einer Aktiv/Aktiv-Bereitstellung über zwei Zonen könnte wie folgt aussehen:

![Aktiv/Aktiv-Zonenbereitstellung](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Die folgenden Überlegungen gelten für diese Konfiguration:

- Wenn Sie keine [Azure-Näherungsplatzierungsgruppe](../../linux/co-location.md) verwenden, behandeln Sie die Azure-Verfügbarkeitszonen als Fehler- und Updatedomänen für alle virtuellen Computer, da Verfügbarkeitsgruppen nicht in Azure-Verfügbarkeitszonen bereitgestellt werden können.
- Wenn Sie zonale Bereitstellungen für die DBMS-Schicht und zentrale Dienste kombinieren möchten, aber Azure-Verfügbarkeitsgruppen für die Anwendungsschicht verwenden möchten, müssen Sie Azure-Näherungsplatzierungsgruppen verwenden, wie im Artikel [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](sap-proximity-placement-scenarios.md) beschrieben.
- Die Lastenausgleichsmodule für die Failovercluster von SAP Central Services und die DBMS-Ebene müssen [Azure Load Balancer-Instanzen der Standard-SKU](../../../load-balancer/load-balancer-standard-availability-zones.md) sein. Der Load Balancer Basic funktioniert nicht zonenübergreifend.
- Das virtuelle Azure-Netzwerk, das Sie zum Hosten des SAP-Systems bereitgestellt haben, wird gemeinsam mit seinen Subnetzen über Zonen verteilt. Sie benötigen keine separaten virtuellen Netzwerke für jede Zone.
- Für alle VMs, die Sie bereitstellen, müssen Sie [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) verwenden. Nicht verwaltete Datenträger werden für zonale Bereitstellungen nicht unterstützt.
- Azure Storage Premium und [SSD Ultra-Speicher](../../disks-types.md#ultra-disk) unterstützen keinen zonenübergreifenden Speicherreplikationstyp. Die Anwendung (DBMS oder SAP Central Services) muss wichtige Daten replizieren.
- Das gilt auch für das freigegebene Verzeichnis „sapmnt“, das ein freigegebener Datenträger (Windows), eine CIFS-Freigabe (Windows) oder NFS-Freigabe (Linux) ist. Sie müssen eine Technologie verwenden, die diese freigegebenen Datenträger oder Dateifreigaben zwischen den Zonen repliziert. Die folgenden Technologien werden unterstützt:
  - Für Windows: eine Clusterlösung, die SIOS DataKeeper verwendet, wie in [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure](./sap-high-availability-guide-wsfc-shared-disk.md) dokumentiert.
  - Für SUSE Linux: eine NFS-Freigabe, die wie in [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md) dokumentiert erstellt wird.
    
    Aktuell wird die Lösung mit Microsoft-Dateiservern mit horizontaler Skalierung, wie in [Vorbereiten der Azure-Infrastruktur für SAP-Hochverfügbarkeit mit einem Windows-Failovercluster und einer Dateifreigabe für SAP ASCS-/SCS-Instanzen](./sap-high-availability-infrastructure-wsfc-file-share.md) dokumentiert, nicht zonenübergreifend unterstützt.
- Die dritte Zone wird verwendet, um das SBD-Gerät zu hosten, falls Sie einen [SUSE Linux Pacemaker-Cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) oder zusätzliche Anwendungsinstanzen erstellen.
- Für Laufzeitkonsistenz für unternehmenskritische Prozesse können Sie versuchen, bestimmte Batchaufträge und Benutzer mithilfe von SAP Batch Server-Gruppen, SAP-Anmeldegruppen oder RFC-Gruppen direkt zu Anwendungsinstanzen in derselben Zone wie die aktive DBMS-Instanz zu leiten. Im Fall eines zonalen Failovers müssen Sie diese Gruppen jedoch manuell in Instanzen verschieben, die auf VMs in der Zone mit der aktiven DB-VM ausgeführt werden.  
- Möglicherweise sollten Sie ruhende Dialoginstanzen in jeder Zone bereitstellen. Auf diese Weise findet eine sofortige Rückkehr zur vorherigen Ressourcenkapazität statt, wenn eine Zone, die von Ihren Anwendungsinstanzen verwendet wird, außer Betrieb ist.

> [!IMPORTANT]
> In diesem Aktiv/Aktiv-Szenario werden zusätzliche Gebühren für Bandbreite von Microsoft ab 01.04.2020 angekündigt. Lesen Sie das Dokument [Preisdetails zur Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/). Die Datenübertragung zwischen der SAP-Anwendungsschicht und der SAP-DBMS-Schicht ist recht kostenintensiv. Daher kann das Aktiv/Aktiv-Szenario erheblich zu den Kosten beitragen. Kehren Sie regelmäßig zu diesem Artikel zurück, um sich über die genauen Kosten zu informieren. 


## <a name="activepassive-deployment"></a>Aktiv/Passiv-Bereitstellung
Falls Sie kein akzeptables Delta zwischen der Netzwerklatenz in einer Zone und der Latenz des zonenübergreifenden Netzwerverkehrs feststellen, können Sie eine Architektur bereitstellen, die aus Sicht der SAP-Anwendungsschicht einen Aktiv/Passiv-Charakter aufweist. Sie definieren eine *aktive* Zone – die Zone, in der Sie die vollständige Anwendungsschicht bereitstellen und versuchen, die aktive DBMS- und die SAP Central Services-Instanz auszuführen. Mit einer solchen Konfiguration müssen Sie sicherstellen, dass kein extremer Laufzeitunterschied in Geschäftstransaktionen und Batchaufträgen auftritt, wenn ein Auftrag in der Zone mit der aktiven DBMS-Instanz ausgeführt wird bzw. das nicht der Fall ist.

Das grundlegende Layout der Architektur sieht folgendermaßen aus:

![Aktiv/Passiv-Zonenbereitstellung](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Die folgenden Überlegungen gelten für diese Konfiguration:

- Verfügbarkeitsgruppen können nicht in Azure-Verfügbarkeitszonen bereitgestellt werden. Um dies zu kompensieren, können Sie Azure-Näherungsplatzierungsgruppen wie im Artikel [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](sap-proximity-placement-scenarios.md) dokumentiert verwenden.
- Wenn Sie diese Architektur verwenden, müssen Sie den Status genau überwachen und versuchen, die aktiven DBMS- und SAP Central Services-Instanzen in derselben Zone zu halten wie Ihre bereitgestellte Anwendungsschicht. Im Fall eines Failovers der SAP Central Services- oder DBMS-Instanz müssen Sie sicherstellen, dass Sie so schnell wie möglich ein manuelles Failback in die Zone mit der SAP-Anwendungsschicht durchführen können.
- Die Lastenausgleichsmodule für die Failovercluster von SAP Central Services und die DBMS-Ebene müssen [Azure Load Balancer-Instanzen der Standard-SKU](../../../load-balancer/load-balancer-standard-availability-zones.md) sein. Der Load Balancer Basic funktioniert nicht zonenübergreifend.
- Das virtuelle Azure-Netzwerk, das Sie zum Hosten des SAP-Systems bereitgestellt haben, wird gemeinsam mit seinen Subnetzen über Zonen verteilt. Sie benötigen keine separaten virtuellen Netzwerke für jede Zone.
- Für alle VMs, die Sie bereitstellen, müssen Sie [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) verwenden. Nicht verwaltete Datenträger werden für zonale Bereitstellungen nicht unterstützt.
- Azure Storage Premium und [SSD Ultra-Speicher](../../disks-types.md#ultra-disk) unterstützen keinen zonenübergreifenden Speicherreplikationstyp. Die Anwendung (DBMS oder SAP Central Services) muss wichtige Daten replizieren.
- Das gilt auch für das freigegebene Verzeichnis „sapmnt“, das ein freigegebener Datenträger (Windows), eine CIFS-Freigabe (Windows) oder NFS-Freigabe (Linux) ist. Sie müssen eine Technologie verwenden, die diese freigegebenen Datenträger oder Dateifreigaben zwischen den Zonen repliziert. Die folgenden Technologien werden unterstützt:
    - Für Windows: eine Clusterlösung, die SIOS DataKeeper verwendet, wie in [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure](./sap-high-availability-guide-wsfc-shared-disk.md) dokumentiert.
    - Für SUSE Linux: eine NFS-Freigabe, die wie in [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md) dokumentiert erstellt wird.
    
  Aktuell wird die Lösung mit Microsoft-Dateiservern mit horizontaler Skalierung, wie in [Vorbereiten der Azure-Infrastruktur für SAP-Hochverfügbarkeit mit einem Windows-Failovercluster und einer Dateifreigabe für SAP ASCS-/SCS-Instanzen](./sap-high-availability-infrastructure-wsfc-file-share.md) dokumentiert, nicht zonenübergreifend unterstützt.
- Die dritte Zone wird verwendet, um das SBD-Gerät zu hosten, falls Sie einen [SUSE Linux Pacemaker-Cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) oder zusätzliche Anwendungsinstanzen erstellen.
- Sie sollten ruhende virtuelle Computer in der (aus DBMS-Perspektive) passiven Zone bereitstellen, damit Sie nach einem Zonenausfall Anwendungsressourcen starten können.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) kann derzeit aktive virtuelle Computer nicht auf ruhende virtuelle Computern zwischen Zonen replizieren. 
- Sie sollten in Automatisierung investieren, die Ihnen beim Ausfall einer Zone den automatischen Start der SAP-Anwendungsschicht in der zweiten Zone ermöglicht.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinierte Konfiguration von Hochverfügbarkeit und Notfallwiederherstellung
Microsoft teilt keine Informationen zur geografischen Entfernung zwischen den Einrichtungen, die verschiedene Azure-Verfügbarkeitszonen in einer Azure-Region hosten. Trotzdem nutzen einige Kunden Zonen für eine kombinierte Hochverfügbarkeits- und Notfallwiederherstellungskonfiguration, die ein Recovery Point Objective (RPO) von null verspricht. Das bedeutet, dass Sie selbst bei der Notfallwiederherstellung keine committeten Datenbanktransaktionen verlieren sollten. 

> [!NOTE]
> Es wird empfohlen, eine solche Konfiguration nur unter bestimmten Umständen zu verwenden. Beispielsweise können Sie sie verwenden, wenn Daten die Azure-Region aus Sicherheits- oder Compliancegründen nicht verlassen dürfen. 

Hier ist ein Beispiel für eine solche Konfiguration:

![Kombinierte Hochverfügbarkeit und Notfallwiederherstellung in Zonen](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Die folgenden Überlegungen gelten für diese Konfiguration:

- Entweder nehmen Sie an, dass eine signifikante Distanz zwischen den Einrichtungen, die eine Verfügbarkeitszone hosten, liegt, oder Sie sind auf eine bestimmte Azure-Region beschränkt. Verfügbarkeitsgruppen können nicht in Azure-Verfügbarkeitszonen bereitgestellt werden. Um dies zu kompensieren, können Sie Azure-Näherungsplatzierungsgruppen wie im Artikel [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](sap-proximity-placement-scenarios.md) dokumentiert verwenden.
- Wenn Sie diese Architektur verwenden, müssen Sie den Status genau überwachen und versuchen, die aktiven DBMS- und SAP Central Services-Instanzen in derselben Zone zu halten wie Ihre bereitgestellte Anwendungsschicht. Im Fall eines Failovers der SAP Central Services- oder DBMS-Instanz müssen Sie sicherstellen, dass Sie so schnell wie möglich ein manuelles Failback in die Zone mit der SAP-Anwendungsschicht durchführen können.
- Sie sollten die Produktionsanwendungsinstanzen auf den virtuellen Computern, auf denen die aktiven QA-Anwendungsinstanzen ausgeführt werden, vorinstalliert haben.
- Beim Ausfall einer Zone fahren Sie die QA-Anwendungsinstanzen herunter und starten stattdessen die Produktionsinstanzen. Beachten Sie, dass Sie virtuelle Namen für die Anwendungsinstanzen verwenden müssen, damit dies funktioniert.
- Die Lastenausgleichsmodule für die Failovercluster von SAP Central Services und die DBMS-Ebene müssen [Azure Load Balancer-Instanzen der Standard-SKU](../../../load-balancer/load-balancer-standard-availability-zones.md) sein. Der Load Balancer Basic funktioniert nicht zonenübergreifend.
- Das virtuelle Azure-Netzwerk, das Sie zum Hosten des SAP-Systems bereitgestellt haben, wird gemeinsam mit seinen Subnetzen über Zonen verteilt. Sie benötigen keine separaten virtuellen Netzwerke für jede Zone.
- Für alle VMs, die Sie bereitstellen, müssen Sie [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) verwenden. Nicht verwaltete Datenträger werden für zonale Bereitstellungen nicht unterstützt.
- Azure Storage Premium und [SSD Ultra-Speicher](../../disks-types.md#ultra-disk) unterstützen keinen zonenübergreifenden Speicherreplikationstyp. Die Anwendung (DBMS oder SAP Central Services) muss wichtige Daten replizieren.
- Das gilt auch für das freigegebene Verzeichnis „sapmnt“, das ein freigegebener Datenträger (Windows), eine CIFS-Freigabe (Windows) oder NFS-Freigabe (Linux) ist. Sie müssen eine Technologie verwenden, die diese freigegebenen Datenträger oder Dateifreigaben zwischen den Zonen repliziert. Die folgenden Technologien werden unterstützt:
    - Für Windows: eine Clusterlösung, die SIOS DataKeeper verwendet, wie in [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure](./sap-high-availability-guide-wsfc-shared-disk.md) dokumentiert.
    - Für SUSE Linux: eine NFS-Freigabe, die wie in [Hochverfügbarkeit für NFS auf Azure-VMs unter SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md) dokumentiert erstellt wird.

  Aktuell wird die Lösung mit Microsoft-Dateiservern mit horizontaler Skalierung, wie in [Vorbereiten der Azure-Infrastruktur für SAP-Hochverfügbarkeit mit einem Windows-Failovercluster und einer Dateifreigabe für SAP ASCS-/SCS-Instanzen](./sap-high-availability-infrastructure-wsfc-file-share.md) dokumentiert, nicht zonenübergreifend unterstützt.
- Die dritte Zone wird verwendet, um das SBD-Gerät zu hosten, falls Sie einen [SUSE Linux Pacemaker-Cluster](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) oder zusätzliche Anwendungsinstanzen erstellen.





## <a name="next-steps"></a>Nächste Schritte
Hier sind einige der nächsten Schritte für die Bereitstellung über Azure-Verfügbarkeitszonen hinweg:

- [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Vorbereiten der Azure-Infrastruktur für SAP-Hochverfügbarkeit mit einem Windows-Failovercluster und einer Dateifreigabe für SAP ASCS-/SCS-Instanzen](./sap-high-availability-infrastructure-wsfc-file-share.md)