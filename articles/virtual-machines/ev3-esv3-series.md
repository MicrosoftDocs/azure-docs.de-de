---
title: Ev3-Serie und Esv3-Serie
description: Hier finden Sie die Spezifikationen für virtuelle Computer der Ev3-Serie und der Esv3-Serie.
author: DavidCBerry13
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: daberry
ms.openlocfilehash: 537a301ef2c3ba1bb5ed34d92c8784655c79d97f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097471"
---
# <a name="ev3-and-esv3-series"></a>Ev3- und Esv3-Serie

Die Serien Ev3 und Esv3 werden auf dem Intel® Xeon® Platinum-Prozessor 8272CL (Cascade Lake), dem Intel® Xeon®-Prozessor 8171M mit 2,1 GHz (Skylake) oder dem Intel® Xeon®-Prozessor E5-2673 v4 mit 2,3 GHz (Broadwell) in einer Hyperthreadkonfiguration ausgeführt und bieten ein besseres Preis-Leistungs-Verhältnis für die meisten universellen Workloads und besseren Einklang von Ev3 mit den universellen VMs der meisten anderen Clouds.  Der Speicher wurde erweitert (von 7 GiB/vCPU auf 8 GiB/vCPU), während die Datenträger- und Netzwerkgrenzwerte pro Kern angepasst wurden, um sich für den Übergang zum Hyperthreading anzupassen. Die Ev3-Serie ist der Nachfolger für die VMs mit großen Arbeitsspeichergrößen der D/Dv2-Familien.

## <a name="ev3-series"></a>Ev3-Serie

Instanzen der Ev3-Serie werden auf dem Intel® Xeon® Platinum-Prozessor 8272CL (Cascade Lake), dem Intel® Xeon®-Prozessor 8171M mit 2,1 GHz (Skylake) oder dem Intel® Xeon®-Prozessor E5-2673 v4 mit 2,3 GHz (Broadwell) ausgeführt und verfügen über Intel Turbo Boost Technology 2.0. Die Instanzen der ESv3-Serie eignen sich ideal für speicherintensive Unternehmensanwendungen.

Datenträgerspeicher wird separat zu virtuellen Computern abgerechnet. Verwenden Sie die ESv3-Größen, um Datenträger mit Premium-Speicher zu nutzen. Die Preis- und Abrechnungskennzahlen für die ESv3-Größen entsprechen denen der Ev3-Serie.

Virtuelle Computer der Ev3-Serie verfügen über Hyperthreading-Technologie von Intel®.

[ACU:](acu.md) 160 – 190<br>
[Storage Premium:](premium-storage-performance.md) Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Maximale Anzahl NICs/Netzwerkbandbreite |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1600 | 32 | 96000/1000/500 | 8/30000 |

<sup>1</sup> Eingeschränkte Kerngrößen verfügbar

<sup>2</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.

## <a name="esv3-series"></a>Esv3-Serie

Instanzen der Esv3-Serie werden auf dem Intel® Xeon® Platinum-Prozessor 8272CL (Cascade Lake), dem Intel® Xeon®-Prozessor 8171M mit 2,1 GHz (Skylake) oder dem Intel® Xeon®-Prozessor E5-2673 v4 mit 2,3 GHz (Broadwell) ausgeführt, verfügen über Intel Turbo Boost Technology 2.0 und nutzen Storage Premium. Instanzen der ESv3-Serie eignen sich ideal für speicherintensive Unternehmensanwendungen.

Virtuelle Computer der Esv3-Serie verfügen über Hyperthreading-Technologie von Intel®.

[ACU:](acu.md) 160–190<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Zwischengespeicherter Burst und Durchsatz des temporären Speichers: IOPS/Megabits pro Sekunde<sup>3</sup> | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s |  Durchsatz des Datenträgers mit Burst ohne Cache: IOPS/Megabits pro Sekunde<sup>3</sup>| Maximale Anzahl NICs/erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3                | 2  | 16  | 32  | 4  | 4000/32 (50)       | 4000/100    | 3200/48    | 4000/100 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>   | 4  | 32  | 64  | 8  | 8000/64 (100)      | 8000/200    | 6400/96    | 8000/200 | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>   | 8  | 64  | 128 | 16 | 16000/128 (200)    | 16000/400   | 12800/192  | 16000/400 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup>  | 16 | 128 | 256 | 32 | 32000/256 (400)    | 32000/800   | 25600/384  | 32000/800 | 8/8000 |
| Standard_E20s_v3               | 20 | 160 | 320 | 32 | 40000/320 (400)    | 40000/1000  | 32000/480  | 40000/1000 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 64000/1600  | 51200/768  | 64000/1600 | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 96000/2000  | 76800/1152 | 80000/2000 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80.000/2.000 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 128000/2000 | 80000/1200 | 80.000/2.000 | 8/30000 |

<sup>1</sup> Eingeschränkte Kerngrößen verfügbar

<sup>2</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.

<sup>3</sup> VMs der Esv3-Serie können mit einem [Burst](./disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Weitere Größen und Informationen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)
- [Preisrechner](https://azure.microsoft.com/pricing/calculator/)
- Weitere Informationen zu Datenträgertypen finden Sie unter [Welche Datenträgertypen stehen in Azure zur Verfügung?](disks-types.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.