---
title: 'M-Serie: Azure Virtual Machines'
description: Hier finden Sie Spezifikationen für die virtuellen Computer der M-Serie.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: a8f39b5a1994574fa3d1472a2b53a3b080f82952
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089240"
---
# <a name="m-series"></a>M-Serie

Die M-Serie verfügt über eine hohe vCPU-Anzahl (bis zu 128 vCPUs) und eine große Menge von Arbeitsspeicher (bis zu 3,8 TiB). Dies ist auch ideal für extrem große Datenbanken oder andere Anwendungen, für die eine hohe vCPU-Anzahl und große Mengen an Arbeitsspeicher benötigt werden. Die Größen der M-Serie werden sowohl unter der Intel&reg; Xeon&reg;-CPU E7-8890 v3 mit 2,50 GHz als auch unter Intel&reg; Xeon&reg; Platinum 8280M (Cascade Lake) unterstützt.

Virtuelle Computer der M-Serie verfügen über Hyperthreading-Technologie von Intel&reg;.

[ACU:](acu.md) 160-180<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Schreibbeschleunigung:](./how-to-enable-write-accelerator.md) Unterstützt<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_M8ms                    | 8   | 218,75 | 256   | 8  | 10.000/100 (793)     | 5000/125   | 4|2000  |
| Standard_M16ms                   | 16  | 437,5  | 512   | 16 | 20.000/200 (1587)    | 10.000/250  | 8|4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40.000/400 (3174)    | 20.000/500  | 8|8.000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40.000/400 (3174)    | 20.000/500  | 8|8.000  |
| Standard_M32ms                   | 32  | 875    | 1024  | 32 | 40.000/400 (3174)    | 20.000/500  | 8|8.000  |
| Standard_M64s <sup>1</sup>       | 64  | 1024   | 2048  | 64 | 80.000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2048  | 64 | 80.000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ms <sup>1</sup>      | 64  | 1792   | 2048  | 64 | 80.000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M128s <sup>1</sup>    | 128 | 2048   | 4096  | 64 | 160.000/1.600 (12696) | 80.000/2.000 | 8|30.000 |
| Standard_M128ms <sup>1,2</sup>   | 128 | 3.892   | 4096  | 64 | 160.000/1.600 (12696) | 80.000/2.000 | 8|30.000 |
| Standard_M64 <sup>1</sup>        | 64  | 1024   | 7168  | 64 | 80.000/800 (1228)    | 40.000/1.000 | 8|16000 |
| Standard_M64m <sup>1</sup>       | 64  | 1792   | 7168  | 64 | 80.000/800 (1228)    | 40.000/1.000 | 8|16000 |
| Standard_M128 <sup>1</sup>     | 128 | 2048   | 14336 | 64 | 250.000/1.600 (2456)  | 80.000/2.000 | 8|32000 |
| Standard_M128m <sup>1</sup>    | 128 | 3.892   | 14336 | 64 | 250.000/1.600 (2456)  | 80.000/2.000 | 8|32000 |

<sup>1</sup> Bei mehr als 64 vCPUs ist eine der folgenden Versionen des Gastbetriebssystems erforderlich: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 und Red Hat Enterprise Linux, CentOS 7.3 oder Oracle Linux 7.3 mit LIS 4.2.1.

<sup>2</sup> Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.

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