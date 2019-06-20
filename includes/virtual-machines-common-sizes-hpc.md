---
title: include file
description: include file
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 9b4bdee19c883252e7de140ac7b19babd43d1df8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755184"
---
Virtuelle Azure-Computer (Azure-VMs) der H-Serie bieten eine herausragende Leistung, MPI-Skalierbarkeit und Kosteneffizienz für verschiedene HPC-Workloads in der Praxis.

VMs der HB-Serie sind für Anwendungen mit hohen Anforderungen an die Speicherbandbreite optimiert, z.B. Strömungssimulationen, explizite Finite-Element-Analysen oder Wettermodelle. HB-VMs bieten 60 AMD-Prozessorkerne des Modells EPYC 7551, 4 GB RAM pro CPU-Kern und kein Hyperthreading. Die AMD EPYC-Plattform bietet eine Speicherbandbreite von mehr als 260 GB/Sekunde.

Die VMs der HC-Serie sind für Anwendungen mit hohen Anforderungen an die Rechenleistung optimiert, z. B. implizite Finite-Element-Analysen, Molekulardynamik oder Chemoinformatik. HC-VMs bieten 44 Intel-Prozessorkerne des Modells Xeon Platinum 8168, 8 GB RAM pro CPU-Kern und kein Hyperthreading. Die Intel Xeon Platinum-Plattform unterstützt Intels umfangreiches Ökosystem von Softwaretools, etwa die Intel Math Kernel Library.

Sowohl HB- als auch HC-VMs unterstützen Mellanox EDR InfiniBand mit 100 GBit/s in einer FAT-Strukturkonfiguration ohne Blocks für einheitliche RDMA-Leistung. HB-und HC-VMs unterstützen Mellanox/OFED-Standardtreiber so, dass alle MPI-Typen und -Versionen sowie RDMA-Verben unterstützt werden.

Virtuelle Computer der H-Serie sind für Anwendungen optimiert, für die Anforderungen hinsichtlich hoher CPU-Frequenzen oder sehr viel Arbeitsspeicher pro Kern gelten. Virtuelle Computer der H-Serie bieten 8 oder 16 Intel-Prozessorkerne des Modells Xeon E5 2667 v3, 7 oder 14 GB RAM pro CPU-Kern und kein Hyperthreading. Die H-Serie unterstützt Mellanox EDR InfiniBand mit 56 GBit/s in einer FAT-Strukturkonfiguration ohne Blocks für einheitliche RDMA-Leistung. Virtuelle Computer der H-Serie unterstützen Intel MPI 5.x und MS-MPI.

## <a name="hb-series"></a>HB-Serie

ACU: 199-216

Storage Premium Unterstützt

Storage Premium-Zwischenspeicherung: Unterstützt

| Größe | vCPU | Prozessor | Arbeitsspeicher (GB) | Speicherbandbreite GB/s | Basis-CPU-Frequenz (GHz) | Frequenz für alle Kerne (GHz, Spitze) | Frequenz für Einzelkern (GHz, Spitze) | RDMA-Leistung (GB/s) | MPI-Unterstützung | Temporärer Speicher (GB) | Max. Anzahl Datenträger | Max. Ethernet-Karten |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Alle | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC-Serie

ACU: 297-315

Storage Premium Unterstützt

Storage Premium-Zwischenspeicherung: Unterstützt


| Größe | vCPU | Prozessor | Arbeitsspeicher (GB) | Speicherbandbreite GB/s | Basis-CPU-Frequenz (GHz) | Frequenz für alle Kerne (GHz, Spitze) | Frequenz für Einzelkern (GHz, Spitze) | RDMA-Leistung (GB/s) | MPI-Unterstützung | Temporärer Speicher (GB) | Max. Anzahl Datenträger | Max. Ethernet-Karten |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3,7 | 100 | Alle | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H-Reihe

ACU: 290-300

Storage Premium  Nicht unterstützt

Storage Premium-Zwischenspeicherung:  Nicht unterstützt

| Größe | vCPU | Prozessor | Arbeitsspeicher (GB) | Speicherbandbreite GB/s | Basis-CPU-Frequenz (GHz) | Frequenz für alle Kerne (GHz, Spitze) | Frequenz für Einzelkern (GHz, Spitze) | RDMA-Leistung (GB/s) | MPI-Unterstützung | Temporärer Speicher (GB) | Max. Anzahl Datenträger | Max. Ethernet-Karten |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> Für MPI-Anwendungen ist ein dediziertes RDMA-Back-End-Netzwerk durch ein FDR InfiniBand-Netzwerk aktiviert.

<br>
