---
title: NVv4-Serie
description: Spezifikationen für die VMs der NVv4-Serie
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: c751f67e9f442669ff3f6828de0a5b350d1706fa
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097706"
---
# <a name="nvv4-series"></a>NVv4-Serie 

Die virtuellen Computer der NVv4-Serie basieren auf GPUs vom Typ [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) sowie auf CPUs vom Typ AMD EPYC 7V12 (Rome). Mit der NVv4-Serie führt Azure virtuelle Computer mit partiellen GPUs ein. Wählen Sie den virtuellen Computer mit der passenden Größe für GPU-beschleunigte Grafikanwendungen und virtuelle Desktops aus – angefangen bei einer Achtel-GPU mit 2 GiB Framepuffer bis hin zu einer vollständigen GPU mit 16 GiB Framepuffer. Von virtuellen Computern der NVv4-Serie wird derzeit nur das Windows-Gastbetriebssystem unterstützt.

<br>

[ACU:](acu.md) 230 – 260<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2/1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4/2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8/4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> Virtuelle Computer der NVv4-Serie verfügen über AMD-Technologie für gleichzeitiges Multithreading.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Unterstützte Betriebssysteme und Treiber

Um die GPU-Funktionen von virtuellen Computern der Azure NVv4-Serie unter Windows nutzen zu können, müssen AMD-GPU-Treiber installiert werden.

Für die manuelle Installation von AMD-GPU-Treibern finden Sie Informationen zu unterstützten Betriebssystemen, Treibern und Installation sowie Schritte zur Überprüfung unter [Einrichten von AMD-GPU-Treibern der N-Serie für Windows](./windows/n-series-amd-driver-setup.md).

## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
