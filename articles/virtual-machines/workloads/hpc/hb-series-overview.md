---
title: Übersicht über virtuelle Computer der HB-Serie – Azure Virtual Machines | Microsoft-Dokumentation
description: Hier finden Sie Informationen zur Unterstützung der Vorschauversion für die VM-Größe der HB-Serie in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 1e37f13bc6e819079c63c583a1eeda12af5e213c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965458"
---
# <a name="hb-series-virtual-machines-overview"></a>Übersicht über virtuelle Computer der HB-Serie

Die Maximierung der Leistung von HPC-Anwendungen (High Performance Computing) für AMD EPYC erfordert ein durchdachtes Konzept für die Lokalität des Arbeitsspeichers und die Prozessplatzierung. Im Anschluss finden Sie einen Überblick über die AMD EPYC-Architektur sowie über unsere Implementierung in Azure für HPC-Anwendungen. Der Begriff „pNUMA“ bezieht sich hier auf eine physische NUMA-Domäne; „vNUMA“ steht für eine virtualisierte NUMA-Domäne.

Physisch handelt es sich bei einem Server der [HB-Serie](../../hb-series.md) um zwei EPYC 7551-CPUs mit jeweils 32 Kernen, wodurch sich eine Gesamtanzahl von 64 physischen Kernen ergibt. Diese 64 Kerne sind in 16 pNUMA-Domänen (acht pro Socket) unterteilt, die jeweils vier Kerne umfassen und als CPU-Komplex (CPU Complex, CCX) bezeichnet werden. Jeder CCX verfügt über einen eigenen L3-Cache, der dem Betriebssystem eine pNUMA-/vNUMA-Grenze signalisiert. Ein Paar von benachbarten CCXs teilt sich jeweils den Zugriff auf zwei Kanäle des physischen DRAM. (Server der HB-Serie verfügen über 32 GB DRAM.)

Damit der Azure-Hypervisor über genügend Platz verfügt, um ohne Beeinträchtigung des virtuellen Computers agieren zu können, wird die physische pNUMA-Domäne 0 (der erste CCX) reserviert. Anschließend werden die pNUMA-Domänen 1–15 (die verbleibenden CCX-Einheiten) für den virtuellen Computer zugewiesen. Für den virtuellen Computer steht also Folgendes zur Verfügung:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` Kerne pro virtuellem Computer

Dem virtuellen Computer ist nicht bekannt, dass ihm pNUMA 0 nicht zugewiesen wurde. Der virtuelle Computer interpretiert pNUMA 1–15 als vNUMA 0–14 – mit sieben vNUMA-Instanzen auf vSocket 0 und 8 vNUMA-Instanzen auf vSocket 1. Ihr Betriebssystem sollte trotz dieser Asymmetrie normal starten und funktionieren. Im weiteren Verlauf dieses Leitfadens erfahren Sie, wie Sie MPI-Anwendungen in diesem asymmetrischen NUMA-Layout am besten ausführen.

Eine feste Prozesszuordnung (Process Pinning) funktioniert bei virtuellen Computern der HB-Serie, da der zugrunde liegende Chip unverändert für den virtuellen Gastcomputer verfügbar gemacht wird. Aus Leistungs- und Konsistenzgründen wird dringend empfohlen, Prozesse fest zuzuordnen.

Das folgende Diagramm zeigt die Trennung zwischen den Kernen, die für den Azure-Hypervisor reserviert sind, und den Kernen für den virtuellen Computer der HB-Serie:

![Trennung zwischen den Kernen, die für den Azure-Hypervisor reserviert sind, und den Kernen für den virtuellen Computer der HB-Serie](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardwarespezifikationen

| Hardwarespezifikationen                | Virtueller Computer der HB-Serie                     |
|----------------------------------|----------------------------------|
| Kerne                            | 60 (SMT deaktiviert)                |
| CPU                              | AMD EPYC 7551                    |
| CPU-Frequenz (ohne AVX)          | ~2,55 GHz (einzeln + alle Kerne)   |
| Arbeitsspeicher                           | 4 GB/Kern (240 GB insgesamt)         |
| Lokaler Datenträger                       | 700 GB SSD                       |
| InfiniBand                       | 100 GBit EDR Mellanox ConnectX-5 |
| Netzwerk                          | 50-GBit-Ethernet (davon 40 GBit nutzbar); Azure-SmartNIC der zweiten Generation |

## <a name="software-specifications"></a>Softwarespezifikationen

| Softwarespezifikationen           |Virtueller Computer der HB-Serie           |
|-----------------------------|-----------------------|
| Maximale MPI-Auftragsgröße            | 18000 Kerne (300 VMs in einer einzelnen VM-Skalierungsgruppe mit singlePlacementGroup=true)  |
| MPI-Unterstützung                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| Zusätzliche Frameworks       | Unified Communication X, libfabric, PGAS |
| Azure Storage-Unterstützung       | Standard- und Premium-Datenträger (maximal 4 Datenträger) |
| Betriebssystemunterstützung für SR-IOV/RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Orchestratorunterstützung        | CycleCloud, Batch  |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Architektur von AMD EPYC](https://bit.ly/2Epv3kC) und die [Architekturen mit mehreren Chips](https://bit.ly/2GpQIMb). Ausführlichere Informationen finden Sie im [HPC-Optimierungsleitfaden für AMD EPYC-Prozessoren](https://bit.ly/2T3AWZ9).
- Informieren Sie sich in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute) über die neuesten Ankündigungen, und machen Sie sich mit einigen HPC-Beispielen und Ergebnissen vertraut.
- Eine allgemeinere Architekturübersicht zur Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
