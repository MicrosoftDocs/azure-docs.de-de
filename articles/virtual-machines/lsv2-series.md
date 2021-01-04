---
title: 'Lsv2-Serie: Azure Virtual Machines'
description: Hier finden Sie Spezifikationen für die virtuellen Computer der Lsv2-Serie.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ba971919037230cb6c09e627fde448647063b5b9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498420"
---
# <a name="lsv2-series"></a>Lsv2-Reihe

Die Lsv2-Reihe bietet direkt zugeordneten lokalen NVMe-Speicher mit hohem Durchsatz und geringer Latenz auf dem [AMD EPYC<sup>TM</sup> 7551-Prozessor](https://www.amd.com/en/products/epyc-7000-series) mit einer Turbo-Taktfrequenz von 2,55 GHz für alle Kerne und einer maximalen Turbo-Taktfrequenz von 3,0 GHz. Die VMs der Lsv2-Reihe sind in Größen von 8 bis 80 vCPUs in einer Konfiguration mit simultanem Multithreading verfügbar.  Es stehen 8 GiB Arbeitsspeicher pro vCPU und ein NVMe-SSD-M.2-Gerät mit 1,92 TB pro 8 vCPUs zur Verfügung, mit bis zu 19,2 TB (10x1,92 TB) auf dem L80s v2.

> [!NOTE]
> Die VMs der Lsv2-Reihe sind für die Verwendung des direkt an die VM angebundenen lokalen Datenträgers auf dem Knoten optimiert, statt für die Verwendung dauerhafter Datenträger. Dies ermöglicht höhere IOPs/mehr Durchsatz für Ihre Workloads. Die Reihen „Lsv2“ und „Ls“ unterstützen nicht die Erstellung eines lokalen Caches zum Erhöhen der IOPS, wie über dauerhafte Datenträger erreicht werden könnte.
>
> Dank des hohen Durchsatzes und der IOPS des lokalen Datenträgers eignen sich virtuelle Computer der Lsv2-Serie ideal für NoSQL-Speicher wie Apache Cassandra und MongoDB, die Daten über mehrere virtuelle Computer replizieren, um Persistenz im Falle eines Ausfalls eines einzelnen virtuellen Computers zu erzielen.
>
> Weitere Informationen finden Sie unter „Optimieren der Leistung virtueller Computer der Lsv2-Serie für [Windows](../virtual-machines/windows/storage-performance.md) oder [Linux](../virtual-machines/linux/storage-performance.md)“.  

[ACU:](acu.md) 150–175<br>
[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 1 und 2<br>
Bursting: Unterstützt<br>
<br>

| Size | vCPU | Arbeitsspeicher (GiB) | Temporäre Datenträger<sup>1</sup> (GiB) | NVMe-Datenträger<sup>2</sup> | NVMe-Datenträgerdurchsatz<sup>3</sup> (Lese-IOPS/MBit/s) | Datenträgerdurchsatz ohne Cache (IOPS/MB/s)<sup>4</sup> | Maximaler Burst-Datenträgerdurchsatz ohne Cache: (IOPS/MB/s)<sup>5</sup>| Max. Anzahl Datenträger | Maximale Anzahl NICs | Erwartete Netzwerkbandbreite (MBit/s) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1,92 TB  | 400.000/2.000  | 8\.000/160   | 8\.000/1.280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1,92 TB  | 800.000/4.000  | 16.000/320  | 16.000/1.280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1,92 TB  | 1.500.000/8.000    | 32.000/640  | 32.000/1.280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1,92 TB  | 2.200.000/14.000   | 48.000/960  | 48.000/2.000 | 32 | 8 | 16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1,92 TB  | 2.900.000/16.000   | 64.000/1.280 | 64.000/2.000 | 32 | 8 | 16000+ |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10x1,92 TB | 3.800.000/20.000 | 80.000/1.400 | 80000/2000 | 32 | 8 | 16000+ |

<sup>1</sup> VMs der Lsv2-Reihe verfügen über einen standardmäßigen SCSI-basierten temporären Ressourcendatenträger für die Verwendung als Auslagerungsdatei des Betriebssystems (D: unter Windows, /dev/sdb unter Linux). Dieser Datenträger bietet 80 GiB Speicher, 4.000 IOPS und eine Übertragungsrate von 80 MB/s für jeweils 8 vCPUs (beispielsweise bietet Standard_L80s_v2 800 GiB bei 40.000 IOPS und 800 MB/s). Dadurch wird sichergestellt, dass die NVMe-Laufwerke vollständig für die Anwendungsnutzung reserviert werden können. Dieser Datenträger ist kurzlebig, und beim Beenden oder Aufheben der Zuordnung gehen alle Daten verloren.

<sup>2</sup> Lokale NVMe-Datenträger sind kurzlebig. Die Daten auf diesen Datenträgern gehen verloren, wenn Sie den virtuellen Computer beenden oder seine Zuordnung aufheben.

<sup>3</sup> Hyper-V NVMe Direct-Technologie ermöglicht den ungedrosselten Zugriff auf lokale NVMe-Laufwerke, die sicher dem Bereich der Gast-VM zugeordnet sind.  Für das Erreichen maximaler Leistung sind entweder der neueste WS2019-Build oder Ubuntu 18.04 oder 16.04 aus dem Azure Marketplace erforderlich.  Die Schreibleistung weicht je nach EA-Größe, Laufwerksauslastung und Kapazitätsnutzung ab.

<sup>4</sup> VMs der Lsv2-Reihe stellen keinen Hostcache für Datenträger bereit, da dieser den Lsv2-Workloads nicht zugute kommt.

<sup>5</sup> VMs der Lsv2-Serie können ihre Datenträgerleistung für jeweils bis zu 30 Minuten mit [Burst](./disk-bursting.md) verbessern. 

<sup>6</sup> Für VMs mit mehr als 64 vCPUs ist eines dieser unterstützten Gastbetriebssysteme erforderlich:

- Windows Server 2016 oder höher
- Ubuntu 16.04 LTS oder höher mit für Azure optimiertem Kernel (4.15 Kernel oder höher)
- SLES 12 SP2 oder höher
- RHEL oder CentOS-Version 6.7 bis 6.10 mit Installation des von Microsoft bereitgestellten LIS-Pakets 4.3.1 (oder höher)
- RHEL oder CentOS-Version 7.3 mit Installation des von Microsoft bereitgestellten LIS-Pakets 4.2.1 (oder höher)
- RHEL oder CentOS-Version 7.6 oder höher
- Oracle Linux mit UEK4 oder höher
- Debian 9 mit Backports-Kernel, Debian 10 oder höher
- CoreOS mit 4.14-Kernel oder höher

## <a name="size-table-definitions"></a>Definitionen der Größentabelle

- Speicherkapazität wird in GiB-Einheiten oder 1.024^3 Bytes angezeigt. Beachten Sie beim Vergleich von in GB (1.000^3 Bytes) gemessenen Datenträgern mit in GiB (1.024^3) gemessenen Datenträgern, dass die in GiB angegebenen Kapazitätszahlen kleiner erscheinen können. Beispiel: 1.023 GiB = 1.098,4 GB.
- Der Datenträgerdurchsatz wird in E/A-Vorgängen pro Sekunde (Input/Output Operations Per Second, IOPS) und MB/s gemessen, wobei MB/s = 10^6 Bytes/Sekunde beträgt.
- Die beste Leistung für Ihre virtuellen Computer erhalten Sie, wenn Sie die Anzahl von Datenträgern auf zwei Datenträger pro vCPU beschränken.
- **Erwartete Netzwerkbandbreite** ist die maximale aggregierte [Bandbreite pro VM-Typ](../virtual-network/virtual-machine-network-throughput.md), die NIC-übergreifend für alle Ziele zugeordnet ist. Obergrenzen werden zwar nicht garantiert, können aber bei der Wahl des passenden VM-Typs für die geplante Anwendung als Richtwert herangezogen werden. Die tatsächliche Netzwerkleistung hängt von unterschiedlichen Faktoren ab. Hierzu zählen beispielsweise Netzwerküberlastung, Anwendungslasten und die Netzwerkeinstellungen. Informationen zum Optimieren des Netzwerkdurchsatzes finden Sie unter [Optimieren des Netzwerkdurchsatzes für virtuelle Azure-Computer](../virtual-network/virtual-network-optimize-network-bandwidth.md). Unter Umständen muss eine bestimmte Version gewählt oder der virtuelle Computer optimiert werden, um die erwartete Netzwerkbandbreite unter Linux oder Windows zu erzielen. Weitere Informationen finden Sie unter [Testen der Bandbreite/des Durchsatzes (NTTTCP)](../virtual-network/virtual-network-bandwidth-testing.md).


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