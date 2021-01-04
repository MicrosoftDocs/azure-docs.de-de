---
title: Übersicht über SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Übersicht über die Bereitstellung von SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0144c57549e374404d3dde0fe997ba41adc8606b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967583"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Was ist SAP HANA in Azure (große Instanzen)?

SAP HANA in Azure (große Instanzen) ist eine einzigartige Lösung in Azure. Neben VMs zum Bereitstellen und Ausführen von SAP HANA bietet Azure Ihnen die Möglichkeit, SAP HANA auf Ihnen zugeordneten Bare-Metal-Servern auszuführen und bereitzustellen. Grundlage der Lösung „SAP HANA in Azure (große Instanzen)“ ist dedizierte, d. h. ausschließlich Ihnen zugewiesene Bare-Metal-Host- bzw. -Serverhardware. Die Serverhardware ist in größere Stempel eingebettet, die Infrastruktur für Compute/Server, Netzwerke und Speicher enthalten. Als Kombination ist die Lösung für HANA Tailored Data Center Integration (TDI) zertifiziert. Für SAP HANA in Azure (große Instanzen) sind verschiedene Server-SKUs oder -größen verfügbar. Einheiten können 36 Intel CPU-Kerne und 768 GB Arbeitsspeicher aufweisen. Bis zu 480 Intel CPU-Kerne und 24 TB Arbeitsspeicher sind für Einheiten möglich.

Die Kundenisolation innerhalb des Infrastrukturumfelds wird in Mandanten wie folgt durchgeführt:

- **Netzwerk**: Isolation von Kunden innerhalb des Infrastrukturstapels durch virtuelle Netzwerke pro dem Kunden zugewiesenem Mandanten. Ein Mandant wird einem einzelnen Kunden zugewiesen. Ein Kunde kann mehrere Mandanten haben. Die Netzwerkisolation von Mandanten verhindert die Netzwerkkommunikation zwischen Mandanten auf der Ebene des Infrastrukturumfelds. Dies ist auch dann der Fall, wenn die Mandanten zum selben Kunden gehören.
- **Speicherkomponenten:** Isolation durch Speicher-VMs, denen Speichervolumes zugewiesen sind. Speichervolumen können nur einem virtuellen Speichercomputer zugewiesen werden. Ein virtueller Speichercomputer wird nur einem einzigen Mandanten im SAP HANA-TDI-zertifizierten Infrastrukturstapel zugewiesen. Folglich kann nur in einem bestimmten verknüpften Mandanten auf Speichervolumes zugegriffen werden, die einer Speicher-VM zugewiesen sind. Sie sind zwischen den verschiedenen bereitgestellten Mandanten nicht sichtbar.
- **Server oder Host:** Eine Server- oder Hosteinheit wird nicht zwischen Kunden oder Mandanten freigegeben. Ein Server oder Host, der einem Kunden bereitgestellt wird, ist eine unteilbare Bare-Metal-Computeeinheit, die einem einzigen Mandanten zugewiesen wird. Es wird *keine* Hard- oder Softpartitionierung verwendet, die dazu führen könnte, dass Sie einen Host oder Server für einen anderen Kunden freigeben. Speichervolumen, die dem virtuellen Speichercomputer des spezifischen Kunden zugewiesen sind, werden in einen derartigen Server eingebunden. Einem Mandant kann eine oder mehrere Servereinheiten verschiedener SKUs exklusiv zugewiesen werden.
- In einem Infrastrukturumfeld von SAP HANA in Azure (große Instanzen) werden viele verschiedene Mandanten anhand der Mandantenkonzepte auf Netzwerk-, Speicher- und Computeebene bereitgestellt und voneinander isoliert. 


Diese Bare-Metal-Servereinheiten unterstützen nur das Ausführen von SAP HANA. Die SAP-Anwendungsschicht oder Middlewareschicht der Workload wird auf VMs ausgeführt. Die Infrastrukturumfelder, die die Einheiten von SAP HANA in Azure (große Instanzen) ausführen, sind mit den Backbones der Azure-Netzwerkdienste verbunden. Auf diese Weise wird eine Konnektivität mit geringer Wartezeit zwischen Einheiten von SAP HANA in Azure (große Instanzen) und VMs bereitgestellt.

Seit Juli 2019 wird zwischen den zwei verschiedenen Stempeln der Revisionen von großen HANA-Instanzen und dem Speicherort für Bereitstellungen unterschieden:

- „Revision 3“ (Rev 3): umfasst die Stempel, die vor Juli 2019 für Kunden zur Bereitstellung zur Verfügung gestellt wurden.
- „Revision 4“ (Rev 4): Neuer Stempelentwurf, der in unmittelbarer Nähe zu Azure-VM-Hosts bereitgestellt wird und bisher in den folgenden Azure-Regionen freigegeben wurde:
    -  USA, Westen 2 
    -  East US 
    -  Europa, Westen
    -  Nordeuropa


Dieses Dokument ist eines von mehreren Dokumenten, die sich mit SAP HANA in Azure (große Instanzen) befassen. Es bietet eine Einführung in die grundlegende Architektur, die Zuständigkeiten und die von der Lösung bereitgestellten Dienste. Allgemeine Funktionen der Lösung werden ebenfalls erläutert. Die meisten anderen Bereiche (beispielsweise Netzwerk und Konnektivität) werden in vier weiteren Dokumenten ausführlich behandelt. Die Installation oder Bereitstellung von SAP NetWeaver auf VMs wird in der Dokumentation zu SAP HANA in Azure (große Instanzen) nicht behandelt. SAP NetWeaver in Azure wird in separaten Dokumenten abgedeckt, die sich im selben Azure-Dokumentationscontainer befinden. 


Die anderen Dokumente mit Anleitungen zu großen HANA-Instanzen behandeln die folgenden Bereiche:

- [Übersicht und Architektur von SAP HANA in Azure (große Instanzen)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installieren und Konfigurieren von SAP HANA in Azure (große Instanzen)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Problembehandlung und Überwachung von SAP HANA in Azure (große Instanzen)](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Hochverfügbarkeitskonfiguration unter SUSE mit STONITH](./ha-setup-with-stonith.md)
- [OS backup and restore for Type II SKUs of Revision 3 stamps (Sichern und Wiederherstellen des Betriebssystems für Typ-II-SKUs)](./os-backup-type-ii-skus.md)
- [Sparen bei SAP HANA (große Instanzen) mit einer Azure-Reservierung](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)

**Nächste Schritte**
- Lesen Sie [Informationen zu Begriffen](hana-know-terms.md).
