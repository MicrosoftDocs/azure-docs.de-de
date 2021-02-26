---
title: Ddv4- und Ddsv4-Serie
description: Spezifikationen für die VMs der Dv4-, Ddv4-, Dsv4- und Ddsv4-Serie.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 1606315b6b6cb4cff787a67e97e64baedc6c508e
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097099"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4- und Ddsv4-Serie

Die Ddv4- und Ddsv4-Serie läuft auf Intel-Prozessoren des Typs Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) mit einer Hyperthreading-Konfiguration ausgeführt, die für die meisten Allzweck-Workloads ein besseres Preis-Leistungs-Verhältnis bieten. Sie verfügen über eine Turbo-Taktfrequenz von 3,4 GHz für alle Kerne, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) und [Intel&reg; Advanced Vector Extensions 512 (Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Außerdem unterstützen sie [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Diese neuen VM-Größen bieten 50  % mehr lokalen Speicher sowie bessere IOPS auf lokalen Datenträgern für Lese- und Schreibvorgänge im Vergleich zu den Größen [Dv3/Dsv3](./dv3-dsv3-series.md) mit [Gen2-VMs](./generation-2.md).

Zu den Anwendungsfällen der D-Serie zählen Unternehmensanwendungen, relationale Datenbanken, In-Memory-Caching und Analysen.

## <a name="ddv4-series"></a>Ddv4-Serie

Die Größen der Ddv4-Serie laufen auf dem Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Die Ddv4-Serie bietet eine Kombination aus vCPU, Arbeitsspeicher und temporärem Datenträger, die für die meisten Produktionsworkloads geeignet ist.

Die neuen Ddv4-VM-Größen umfassen schnellen, größeren lokalen SSD-Speicher (bis zu 2.400 GiB) und sind für Anwendungen konzipiert, die von einer niedrigen Latenz und einem schnellen lokalen Speicher profitieren, wie z. B. Anwendungen, die schnelle Lese-/Schreibvorgänge im temporären Speicher oder temporären Speicher für Caches oder temporäre Dateien benötigen. An Ddv4-VMs können Sie SSD Standard- und HDD Standard-Speicher anfügen. Speicher für Remotedatenträger wird separat zu virtuellen Computern abgerechnet.

[ACU](acu.md): 195-210<br>
[Storage Premium:](premium-storage-performance.md) Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
<br> 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | <sup>**</sup> Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19.000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38.500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77.000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154.000/968 | 8|8.000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308.000/1.936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462.000/2.904 | 8|24.000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615.000/3.872 | 8|30.000 |

<sup>**</sup> Diese IOPS-Werte können mithilfe von [Gen2-VMs](generation-2.md) garantiert werden.

## <a name="ddsv4-series"></a>Ddsv4-Serie

Die Ddsv4-Serie läuft auf dem Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Die Ddsv4-Serie bietet eine Kombination aus vCPU, Arbeitsspeicher und temporärem Datenträger, die für die meisten Produktionsworkloads geeignet ist.

Die neuen Ddsv4-VM-Größen umfassen schnellen, größeren lokalen SSD-Speicher (bis zu 2.400 GiB) und sind für Anwendungen konzipiert, die von einer niedrigen Latenz und einem schnellen lokalen Speicher profitieren, wie z. B. Anwendungen, die schnelle Lese-/Schreibvorgänge im temporären Speicher oder temporären Speicher für Caches oder temporäre Dateien benötigen. 

 > [!NOTE]
 >Für Ddsv4-Größen gelten die gleichen Preise und Verbrauchseinheiten für die Abrechnung wie bei der Ddsv4-Serie.

[ACU](acu.md): 195-210<br>
[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Unterstützt <br>
<br> 

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | <sup>**</sup> Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19.000/120(50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38.500/242(100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77.000/485(200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154.000/968(400) | 25600/384 | 8|8.000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308.000/1.936(800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462.000/2.904(1.200) | 76800/1152 | 8|24.000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615.000/3.872(1.600) | 80000/1200 | 8|30.000 |

<sup>**</sup> Diese IOPS-Werte können mithilfe von [Gen2-VMs](generation-2.md) garantiert werden.

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
