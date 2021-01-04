---
title: 'Ev4- und Esv4-Serie: Azure Virtual Machines'
description: Hier finden Sie Spezifikationen für die virtuellen Computer der Ev4- und Esv4-Serie.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 38020b38ebd879bf19c243c2055e73198e9fcc7c
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96606921"
---
# <a name="ev4-and-esv4-series"></a>Ev4- und Esv4-Serie

Die Ev4- und Esv4-Serie werden auf den Prozessoren Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) in einer Hyperthreadingkonfiguration ausgeführt, eignen sich ideal für verschiedene arbeitsspeicherintensive Unternehmensanwendungen und unterstützen zu bis zu 504 GiB RAM. Sie verfügt über eine Turbo-Taktfrequenz von 3,4 GHz auf allen Kernen.

> [!NOTE]
> Häufig gestellte Fragen finden Sie unter [Azure-VM-Größen ohne lokale temporäre Datenträger](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Ev4-Serie

Die Größen der Ev4-Serie laufen auf dem Intel Xeon&reg; Platinum 8272CL (Cascade Lake). Instanzen der Ev4-Serie eignen sich ideal für arbeitsspeicherintensive Unternehmensanwendungen. Virtuelle Computer der Ev4-Serie verfügen über Hyperthreading-Technologie von Intel&reg;.

Speicher für Remotedatenträger wird separat zu virtuellen Computern abgerechnet. Verwenden Sie die Esv4-Größen, um Datenträger mit Premium-Speicher zu nutzen. Die Preis- und Abrechnungskennzahlen für die Esv4-Größen entsprechen denen der Ev4-Serie.

[ACU](acu.md): 195–210<br>
[Storage Premium](premium-storage-performance.md): Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1<br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Nur Remotespeicher | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Nur Remotespeicher | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Nur Remotespeicher | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Nur Remotespeicher | 32 | 8|8.000 |
| Standard_E20_v4 | 20 | 160 | Nur Remotespeicher | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Nur Remotespeicher | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Nur Remotespeicher | 32 | 8|24.000 |
| Standard_E64_v4 | 64 | 504 | Nur Remotespeicher | 32| 8|30.000 |


## <a name="esv4-series"></a>Esv4-Serie

Die Größen der Esv4-Serie laufen auf dem Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake). Instanzen der Esv4-Serie eignen sich ideal für arbeitsspeicherintensive Unternehmensanwendungen. Virtuelle Computer der Evs4-Serie verfügen über Hyperthreading-Technologie von Intel&reg;. Speicher für Remotedatenträger wird separat zu virtuellen Computern abgerechnet.

[ACU](acu.md): 195-210<br>
[Storage Premium](premium-storage-performance.md): Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration](maintenance-and-updates.md): Unterstützt<br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt<br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1 und 2<br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBit/s | Maximale Anzahl NICs|Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Nur Remotespeicher | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Nur Remotespeicher | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Nur Remotespeicher | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Nur Remotespeicher | 32 | 25600/384 | 8|8.000 |
| Standard_E20s_v4 | 20 | 160 | Nur Remotespeicher | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Nur Remotespeicher | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Nur Remotespeicher | 32 | 76800/1152 | 8|24.000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Nur Remotespeicher | 32 | 80000/1200 | 8|30.000 |
| Standard_E80is_v4 <sup>2</sup> | 80 | 504 | Nur Remotespeicher | 32 | 80000/1200 | 8|30.000 |

<sup>1</sup> [Eingeschränkte Kerngrößen verfügbar](./constrained-vcpu.md).

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
