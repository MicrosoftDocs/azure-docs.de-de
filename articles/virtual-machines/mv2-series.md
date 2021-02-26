---
title: 'Mv2-Serie: Azure Virtual Machines'
description: Hier finden Sie Spezifikationen für die virtuellen Computer der Mv2-Serie.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: 962fb8cbd4c64aa2ef625b478afa587099234617
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99427633"
---
# <a name="mv2-series"></a>Mv2-Serie

Die Mv2-Serie verfügt über eine Plattform mit hohem Durchsatz und geringer Latenz, die auf einem Prozessor vom Typ Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) mit Hyperthreading ausgeführt wird, der eine Basistaktfrequenz von 2,5 GHz für alle Kerne und eine maximale Turbotaktfrequenz von 3,8 GHz aufweist. Für alle VM-Größen der Mv2-Serie können sowohl persistente Standard- als auch Premium-Datenträger verwendet werden. Bei Instanzen der Mv2-Serie handelt es sich um arbeitsspeicheroptimierte VM-Größen zur Bereitstellung einer unvergleichlichen Computeleistung, um große In-Memory-Datenbanken und Workloads zu unterstützen. Sie verfügen über ein hohes Arbeitsspeicher/CPU-Verhältnis, das ideal für relationale Datenbankserver, große Caches und In-Memory-Analysen geeignet ist.

Virtuelle Computer der Mv2-Serie verfügen über Hyperthreading-Technologie von Intel®

[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 2<br>
[Schreibbeschleunigung:](./how-to-enable-write-accelerator.md) Unterstützt<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
<br>

|Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBps (Cachegröße in GiB) | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Maximale Anzahl NICs | Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5.700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11.400 | 8192 | 64 | 250.000/1.600 (14.080) | 80000/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5.700 | 8192 | 64 | 250.000/1.600 (14.080) | 80000/2000 | 8 | 32000 |

<sup>1</sup> VMs der Mv2-Serie sind nur Generation 2 und unterstützen eine Teilmenge der unterstützten Images der Generation 2. Im Folgenden finden Sie eine vollständige Liste der unterstützten Images für die Mv2-Serie. Wenn Sie Linux verwenden, finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](./generation-2.md) Anweisungen zum Suchen und Auswählen eines Images. Wenn Sie Windows verwenden, finden Sie unter [Unterstützung für VMs der Generation 2 in Azure](./generation-2.md) Anweisungen zum Suchen und Auswählen eines Images. 

- Windows Server 2019 oder höher
- SUSE Linux Enterprise Server 12 SP4 oder höher, oder SUSE Linux Enterprise Server 15 SP1 oder höher
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 oder höher 
- Oracle Enterprise Linux 7.7 oder höher



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
