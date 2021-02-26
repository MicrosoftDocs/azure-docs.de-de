---
title: 'Virtuelle Computer der arbeitsspeicheroptimierten Dv2- und DSv2-Serie: Azure Virtual Machines'
description: Hier finden Sie die Spezifikationen für virtuelle Computer der Dv2-Serie und der Dsv2-Serie.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: b6f99f1f58faece3ab286b2bc3cb571cdcba87de
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99088903"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Arbeitsspeicheroptimierte Dv2- und Dsv2-Serie

Die Serien Dv2 und Dsv2, Nachfolger der ursprünglichen D-Serie, weisen eine leistungsfähigere CPU auf. Die DSv2-Serie wird auf dem Intel® Xeon® Platinum 8272CL-Prozessor (Cascade Lake), dem Intel® Xeon® 8171M-Prozessor mit 2,1 GHz (Skylake), dem Intel® Xeon® E5-2673 v4-Prozessor mit 2,3 GHz (Broadwell) oder dem Intel® Xeon® E5-2673 v3-Prozessor mit 2,4 GHz (Haswell) ausgeführt. Der Dv2-Serie hat die gleichen Arbeitsspeicher- und Datenträgerkonfigurationen wie die D-Serie.

## <a name="dv2-series-11-15"></a>Dv2-Serie 11-15

Die Dv2-Serie wird auf dem Intel® Xeon® Platinum 8272CL-Prozessor (Cascade Lake), dem Intel® Xeon® 8171M-Prozessor mit 2,1 GHz (Skylake), dem Intel® Xeon® E5-2673 v4-Prozessor mit 2,3 GHz (Broadwell) oder dem Intel® Xeon® E5-2673 v3-Prozessor mit 2,4 GHz (Haswell) ausgeführt.

[ACU:](acu.md) 210–250<br>
[Storage Premium:](premium-storage-performance.md) Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
<br> 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBit/s Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8 x 500   | 2|1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16 x 500 | 4|3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32 x 500 | 8|6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64 x 500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64 x 500 | 8|25000 <sup>2</sup> |

<sup>1</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.
<sup>2</sup> 25000 Mbps mit beschleunigtem Netzwerkbetrieb.

## <a name="dsv2-series-11-15"></a>DSv2-Serie 11-15

Die DSv2-Serie wird auf dem Intel® Xeon® Platinum 8272CL-Prozessor (Cascade Lake), dem Intel® Xeon® 8171M-Prozessor mit 2,1 GHz (Skylake), dem Intel® Xeon® E5-2673 v4-Prozessor mit 2,3 GHz (Broadwell) oder dem Intel® Xeon® E5-2673 v3-Prozessor mit 2,4 GHz (Haswell) ausgeführt.

[ACU:](acu.md) 210–250 <sup>1</sup><br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Unterstützt <br>
<br> 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> Der mit einer VM der DSv2-Serie maximal mögliche Datenträgerdurchsatz (IOPS oder MB/s) kann durch Anzahl, Größe und Striping der angefügten Datenträger beschränkt werden.  Details finden Sie unter [Entwerfen für hohe Leistung](./premium-storage-performance.md).
<sup>2</sup> Die Instanz ist auf der Intel Haswell-basierten Hardware isoliert und ausschließlich für einen einzelnen Kunden vorgesehen.  
<sup>3</sup> Eingeschränkte Kerngrößen verfügbar.  
<sup>4</sup> 25000 Mbps mit beschleunigtem Netzwerkbetrieb.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Weitere Größen und Informationen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

Preisrechner: [Preisrechner](https://azure.microsoft.com/pricing/calculator/)

Weitere Informationen zu Datenträgertypen: [Datenträgertypen](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.