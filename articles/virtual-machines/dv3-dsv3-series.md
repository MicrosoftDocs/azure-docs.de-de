---
title: Dv3- und Dsv3-Serie
description: Hier finden Sie die Spezifikationen für virtuelle Computer der Dv3-Serie und der Dsv3-Serie.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 215f907edb01cc35ffa4e9bb683964f8eda11d94
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089324"
---
# <a name="dv3-and-dsv3-series"></a>Dv3- und Dsv3-Serie

Die Dv3-Serie wird auf dem Intel® Xeon® Platinum 8272CL-Prozessor (Cascade Lake), dem Intel® Xeon® 8171M-Prozessor mit 2,1 GHz (Skylake), dem Intel® Xeon® E5-2673 v4-Prozessor mit 2,3 GHz (Broadwell) oder dem Intel® Xeon® E5-2673 v3-Prozessor mit 2,4 GHz (Haswell) in einer Hyperthreadkonfiguration ausgeführt und bietet somit ein besseres Preis-Leistungs-Verhältnis für die meisten universellen Workloads. Der Speicher wurde erweitert (von etwa 3.5 GiB/vCPU auf 4 GiB/vCPU), während die Datenträger- und Netzwerkgrenzwerte pro Kern angepasst wurden, um sich für den Übergang zum Hyperthreading anzupassen. Die Dv3-Serie hat nicht mehr die virtuellen Computer mit hohen Arbeitsspeichergrößen der D/Dv2-Serie. Diese sind nun in den arbeitsspeicheroptimierten [Serien Ev3 und Esv3](ev3-esv3-series.md) verfügbar.

Zu den möglichen Anwendungsfällen der D-Serie zählen Unternehmensanwendungen, relationale Datenbanken, In-Memory-Caching und Analysen.

## <a name="dv3-series"></a>Dv3-Serie

Die Dv3-Serie wird auf dem Intel® Xeon® Platinum 8272CL-Prozessor (Cascade Lake), dem Intel® Xeon® 8171M-Prozessor mit 2,1 GHz (Skylake), dem Intel® Xeon® E5-2673 v4-Prozessor mit 2,3 GHz (Broadwell) oder dem Intel® Xeon® E5-2673 v3-Prozessor mit 2,4 GHz (Haswell) mit [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) ausgeführt. Die Größen der Dv3-Serie bieten eine Kombination aus vCPU, Arbeitsspeicher und temporärem Speicher für die meisten Produktionsworkloads.

Datenträgerspeicher wird separat zu virtuellen Computern abgerechnet. Verwenden Sie die Dsv3-Größen, um Datenträger mit Premium-Speicher zu nutzen. Die Preis- und Abrechnungskennzahlen für die Dsv3-Größen entsprechen denen der Dv3-Serie.

Virtuelle Computer der Dv3-Serie verfügen über Hyper-Threading-Technologie von Intel®.

[ACU:](acu.md) 160–190<br>
[Storage Premium:](premium-storage-performance.md) Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBit/s Schreiben | Maximale Anzahl NICs/Netzwerkbandbreite |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-Serie

Die Dsv3-Serie wird auf dem Intel® Xeon® Platinum 8272CL-Prozessor (Cascade Lake), dem Intel® Xeon® 8171M-Prozessor mit 2,1 GHz (Skylake), dem Intel® Xeon® E5-2673 v4-Prozessor mit 2,3 GHz (Broadwell) oder dem Intel® Xeon® E5-2673 v3-Prozessor mit 2,4 GHz (Haswell) mit [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) ausgeführt und verwendet Storage Premium. Die Größen der Dsv3-Serie bieten eine Kombination aus vCPU, Arbeitsspeicher und temporärem Speicher für die meisten Produktionsworkloads.

Virtuelle Computer der Dsv3-Serie verfügen über Hyper-Threading-Technologie von Intel®.

[ACU:](acu.md) 160–190<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Zwischengespeicherter maximaler Burst und Durchsatz des temporären Speichers: IOPS/MBit/s<sup>1</sup> | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Durchsatz des Datenträgers mit maximalem Burst ohne Cache: IOPS/MBit/s<sup>1</sup> | Maximale Anzahl NICs/erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80.000/2.000 | 8/30000 |

<sup>1</sup> VMs der Dsv3-Serie können mit einem [Burst](./disk-bursting.md) ihre Datenträgerleistung für jeweils bis zu 30 Minuten auf das maximale Bursting verbessern.

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