---
title: Netzwerkdurchsatz virtueller Azure-Computer | Microsoft-Dokumentation
description: Informationen zum Netzwerkdurchsatz virtueller Azure-Computer.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153726"
---
# <a name="virtual-machine-network-bandwidth"></a>Netzwerkdurchsatz virtueller Computer

Azure bietet eine Vielzahl von VM-Größen und -Typen mit einer unterschiedlichen Kombination von Leistungsaspekten. Zu diesen Leistungsaspekten gehört der Netzwerkdurchsatz (oder Bandbreite), der in Megabit pro Sekunde (MBit/s) gemessen wird. Da virtuelle Computer auf gemeinsam genutzter Hardware gehostet werden, muss die Netzwerkkapazität zwischen den virtuellen Computern, die dieselbe Hardware verwenden, gerecht aufgeteilt werden. Größeren virtuellen Computern wird verhältnismäßig mehr Bandbreite als kleineren virtuellen Computern zugeordnet.
 
Die Netzwerkbandbreite, die dem jeweiligen virtuellen Computer zugeordnet ist, wird anhand des ausgehenden Datenverkehrs vom virtuellen Computer gemessen. Der gesamte Netzwerkdatenverkehr, der vom virtuellen Computer ausgeht, wird unabhängig vom Ziel auf den zugewiesenen Grenzwert angerechnet. Wenn der Grenzwert eines virtuellen Computers beispielsweise bei 1.000 MBit/s liegt, gilt dieser Grenzwert unabhängig davon, ob das Ziel des ausgehenden Datenverkehrs ein anderer virtueller Computer in demselben virtuellen Netzwerk ist oder ob es sich außerhalb von Azure befindet.
 
Der eingehende Datenverkehr wird nicht gemessen oder direkt beschränkt. Es gibt jedoch andere Faktoren, wie z. B. CPU- und Speichergrenzwerte, die sich auf die Fähigkeit eines virtuellen Computers zur Verarbeitung eingehender Daten auswirken können.

Der beschleunigte Netzwerkbetrieb ist ein Feature, das der Verbesserung der Netzwerkleistung, einschließlich Latenz, Durchsatz und CPU-Auslastung, dient. Zwar kann durch den beschleunigten Netzwerkbetrieb der Durchsatz eines virtuellen Computers verbessert werden, doch ist dies nur im Rahmen der Bandbreite möglich, die dem virtuellen Computer zugewiesen ist. Weitere Informationen zur Verwendung des beschleunigten Netzwerkbetriebs finden Sie unter den entsprechenden Abschnitten für virtuelle [Windows](create-vm-accelerated-networking-powershell.md)- oder [Linux](create-vm-accelerated-networking-cli.md)-Computer.
 
Virtuellen Azure-Computern muss eine Netzwerkschnittstelle angefügt sein, doch können es auch mehrere sein. Die einem virtuellen Computer zugewiesene Bandbreite umfasst die Summe des gesamten ausgehenden Datenverkehrs aller Netzwerkschnittstellen, die an einen virtuellen Computer angefügt sind. Anders ausgedrückt: Die zugewiesene Bandbreite gilt pro virtuellem Computer, unabhängig davon, wie viele Netzwerkschnittstellen an den virtuellen Computer angefügt sind. Informationen dazu, wie viele Netzwerkschnittstellen von virtuellen Azure-Computern verschiedener Größen unterstützt werden, finden Sie unter den Größen für virtuelle [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- und [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer in Azure. 

## <a name="expected-network-throughput"></a>Erwartete Netzwerkdurchsatz

Der erwartete ausgehende Durchsatz und die Anzahl der Netzwerkschnittstellen, die von der jeweiligen Größe des virtuellen Computers unterstützt werden, ist unter den Größen für virtuelle [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- und [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer in Azure angegeben. Wählen Sie einen Typ aus (z. B. „Allgemeiner Zweck“), und wählen Sie dann auf der daraufhin angezeigten Seite eine Größenserie aus (z. B. die Dv2-Serie). Jede Serie weist eine Tabelle mit Netzwerkspezifikationen in der letzten Spalte mit dem Titel **Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (Mbps)** auf. 

Die Durchsatzbegrenzung gilt für den virtuellen Computer. Der Durchsatz wird durch folgende Faktoren nicht beeinflusst:
- **Anzahl der Netzwerkschnittstellen**: Der Bandbreitengrenzwert ist ein kumulativer Wert des gesamten ausgehenden Datenverkehrs vom virtuellen Computer.
- **Beschleunigter Netzwerkbetrieb**: Obwohl das Feature beim Erreichen des veröffentlichten Grenzwerts hilfreich sein kann, ändert sich dadurch der Grenzwert nicht.
- **Datenverkehrsziel**: Alle Ziele werden auf den Grenzwert für ausgehenden Datenverkehr angerechnet.
- **Protokoll:** Der gesamte ausgehende Datenverkehr über alle Protokolle wird auf den Grenzwert angerechnet.

## <a name="network-flow-limits"></a>Grenzwerte für Netzwerkflows

Zusätzlich zur Bandbreite kann auch die Anzahl der Netzwerkverbindungen auf einem virtuellen Computer zu einem bestimmten Zeitpunkt Auswirkungen auf die Netzwerkleistung haben. Der Azure-Netzwerkstapel verwaltet den Zustand für jede Richtung einer TCP/UDP-Verbindung in Datenstrukturen mit der Bezeichnung „Flows“. Für eine typische TCP/UDP-Verbindung werden zwei Flows erstellt – einer für die eingehende und einer für die ausgehende Richtung. 

Datenübertragungen zwischen Endpunkten erfordern die Erstellung von mehreren Flows (zusätzlich zu denen für die Datenübertragung). Einige Beispiele sind Flows, die für DNS-Auflösung erstellt werden, und Flows, die für die Integritätstests beim Lastenausgleich erstellt werden. Beachten Sie auch, das für virtuelle Netzwerkgeräte (NVAs) wie Gateways, Proxys und Firewalls Flows für Verbindungen erstellt werden, die auf dem Gerät beendet wurden und von diesem stammen. 

![Anzahl von Flows für die TCP-Konversation über ein Weiterleitungsgerät](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Grenzwerte und Empfehlungen für Flows

Zurzeit unterstützt der Azure-Netzwerkstapel 250.000 Netzwerkflows insgesamt mit guter Leistung für virtuelle Computer mit mehr als 8 CPU-Kernen und 100.000 Flows insgesamt mit guter Leistung für virtuelle Computer mit weniger als 8 CPU-Kernen. Bei Überschreibung dieses Grenzwerts nimmt die Leistung bei weiteren Flows ab. Dies gilt bis zur absoluten Obergrenze von 1 Mio. Flows – 500.000 ein- und 500.000 ausgehend –, ab der weitere Flows verworfen werden.

||VMs mit weniger als 8 CPU-Kernen|VMs mit mehr als 8 CPU-Kernen|
|---|---|---|
|<b>Gute Leistung</b>|100\.000 Flows |250\.000 Flows|
|<b>Abgeminderte Leistung</b>|Über 100.000 Flows|Über 250.000 Flows|
|<b>Grenzwert für Flows</b>|1 Mio. Flows|1 Mio. Flows|

In [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) stehen Metriken zum Nachverfolgen der Anzahl von Netzwerkflows und der Rate der Erstellung von Flows auf Ihren virtuellen Computern oder VMSS-Instanzen zur Verfügung.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Die Raten für die Verbindungsherstellung und -beendigung können sich ebenfalls auf die Netzwerkleistung auswirken, da für die Verbindungsherstellung und -beendigung CPU-Ressourcen mit der Paketverarbeitung geteilt werden. Es wird empfohlen, Benchmarktests für Ihre Workloads mit den zu erwartenden Datenverkehrsmustern durchzuführen und die Workloads entsprechend den Leistungsanforderungen zu erweitern. 

## <a name="next-steps"></a>Nächste Schritte

- [Optimieren des Netzwerkdurchsatzes für das Betriebssystem eines virtuellen Computers](virtual-network-optimize-network-bandwidth.md)
- [Testen des Netzwerkdurchsatzes](virtual-network-bandwidth-testing.md) für einen virtuellen Computer
