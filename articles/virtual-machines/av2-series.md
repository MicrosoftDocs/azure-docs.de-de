---
title: Av2-Serie
description: Hier finden Sie Spezifikationen für die virtuellen Computer der Av2-Serie.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d0972c9ed48f56a495804052c1ffc2b28e1db083
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095323"
---
# <a name="av2-series"></a>Av2-Serie

Die VMs der Av2-Reihe können auf vielen verschiedenen Hardwaretypen und Prozessoren bereitgestellt werden. Die Konfigurationen für CPU-Leistung und Arbeitsspeicher bei virtuellen Computern der Av2-Serie eignen sich am besten für Workloads der Einstiegsebene wie Entwicklung und Tests. Die Größe ist gedrosselt, um eine konsistente Prozessorleistung für die ausgeführte Instanz zu ermöglichen – unabhängig von der für die Bereitstellung gewählten Hardware. Fragen Sie die virtuelle Hardware über die virtuelle Maschine ab, um die physische Hardware zu ermitteln, auf der diese Größe bereitgestellt wird. Einige mögliche Anwendungsfälle: Entwicklungs- und Testserver, Webserver mit geringem Datenverkehr, kleine bis mittelgroße Datenbanken, Proof of Concept und Coderepositorys.

[ACU:](acu.md) 100<br>
[Storage Premium:](premium-storage-performance.md) Nicht unterstützt <br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt <br>
[Livemigration](maintenance-and-updates.md): Unterstützt <br>
[Updates mit Speicherbeibehaltung](maintenance-and-updates.md): Unterstützt <br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1 <br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Nicht unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Nicht unterstützt <br>
<br>

| Size | Virtueller Kern | Memory: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBit/s Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs | Erwartete Netzwerkbandbreite (MBit/s)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1\.000/20/10  | 2/2 x 500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2\.000/40/20  | 4/4 x 500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4\.000/80/40  | 8/8 x 500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8\.000/160/80 | 16/16 x 500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2\.000/40/20  | 4/4 x 500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4\.000/80/40  | 8/8 x 500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8\.000/160/80 | 16/16 x 500 | 8 | 2000 |

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
