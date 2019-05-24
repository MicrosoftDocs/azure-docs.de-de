---
title: Includedatei
description: Includedatei
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: fb829cc5f1eef9c151a70a6479e419076e1c3b24
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170334"
---
Datenspeicheroptimierte VM-Größen bieten hohen Datenträgerdurchsatz und E/A und eignen sich ideal für Big Data, SQL, NoSQL-Datenbanken, Datawarehousing und große transaktionale Datenbanken.  Beispiele bilden Cassandra, MongoDB, Cloudera und Redis. Dieser Artikel enthält Informationen zur Anzahl von vCPUs, Datenträgern und NICs sowie zum lokalen Speicherdurchsatz und zur Netzwerkbandbreite für die einzelnen optimierten Größen.

Die Lsv2-Reihe bietet direkt zugeordneten lokalen NVMe-Speicher mit hohem Durchsatz und geringer Latenz auf dem [AMD EPYC&trade; 7551-Prozessor](https://www.amd.com/en/products/epyc-7000-series) mit einem Basistakt von 2,55 GHz für alle Kerne und einem maximalen Turbotakt von 3,0 GHz. Die VMs der Lsv2-Reihe sind in Größen von 8 bis 80 vCPUs in einer Konfiguration mit simultanem Multithreading verfügbar.  Es stehen 8 GiB Arbeitsspeicher pro vCPU und ein NVMe-SSD-M.2-Gerät mit 1,92 TB pro 8 vCPUs zur Verfügung, mit bis zu 19,2 TB (10x1,92 TB) auf dem L80s v2.

> [!NOTE]
> Die VMs der Lsv2-Reihe sind für die Verwendung des direkt an die VM angebundenen lokalen Datenträgers auf dem Knoten optimiert, statt für die Verwendung dauerhafter Datenträger. Dies ermöglicht höhere IOPs/mehr Durchsatz für Ihre Workloads. Die Reihen „Lsv2“ und „Ls“ unterstützen nicht die Erstellung eines lokalen Caches zum Erhöhen der IOPS, wie über dauerhafte Datenträger erreicht werden könnte.
>
> Dank des hohen Durchsatzes und der IOPS des lokalen Datenträgers eignen sich VMs der Lsv2- und Ls-Reihe ideal für NoSQL-Speicher wie Apache Cassandra und MongoDB, die Daten über mehrere virtuelle Computer replizieren, um Persistenz im Falle eines Ausfalls eines einzelnen virtuellen Computers zu erzielen.
>
> Weitere Informationen finden Sie unter [Optimize performance on the Lsv2-series virtual machines (Optimieren der Leistung von virtuellen Computern der Lsv2-Reihe)](../articles/virtual-machines/linux/storage-performance.md).  


## <a name="lsv2-series"></a>Lsv2-Reihe

ACU: 150–175

Storage Premium Unterstützt

Storage Premium-Zwischenspeicherung: Nicht unterstützt

| Größe          | vCPU | Arbeitsspeicher (GiB) | Temporäre Datenträger<sup>1</sup> (GiB) | NVMe-Datenträger<sup>2</sup> | NVMe-Datenträger-Datendurchsatz<sup>3</sup> (Lese-IOPS/MBit/s) | Maximaler Datenträgerdurchsatz ohne Cache: (IOPS/MB/s)<sup>4</sup> | Max. Anzahl Datenträger | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (MBps) |
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1,92 TB  | 400.000/2.000 | 8.000/160 | 16 | 2/3.200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1,92 TB  | 800.000/4.000 | 16.000/320 | 32 | 4/6.400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1,92 TB  | 1.500.000/8.000    | 32.000/640 | 32 | 8/12.800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1,92 TB  | 2.900.000/16.000   | 64.000/1.280 | 32 | 8/16.600+ |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1,92 TB   | 3.800.000/20.000   | 80.000/1.400 | 32 | 8/16.000+ |

<sup>1</sup> VMs der Lsv2-Reihe verfügen über einen standardmäßigen SCSI-basierten temporären Ressourcendatenträger für die Verwendung als Auslagerungsdatei des Betriebssystems (D: unter Windows, /dev/sdb unter Linux). Dieser Datenträger bietet 80 GiB Speicher, 4.000 IOPS und eine Übertragungsrate von 80 MB/s für jeweils 8 vCPUs (beispielsweise bietet Standard_L80s_v2 800 GiB bei 40.000 IOPS und 800 MB/s). Dadurch wird sichergestellt, dass die NVMe-Laufwerke vollständig für die Anwendungsnutzung reserviert werden können. Dieser Datenträger ist kurzlebig, und beim Beenden oder Aufheben der Zuordnung gehen alle Daten verloren.

<sup>2</sup> Lokale NVMe-Datenträger sind kurzlebig. Die Daten auf diesen Datenträgern gehen verloren, wenn Sie den virtuellen Computer beenden oder seine Zuordnung aufheben.

<sup>3</sup> Hyper-V NVMe Direct-Technologie ermöglicht den ungedrosselten Zugriff auf lokale NVMe-Laufwerke, die sicher dem Bereich der Gast-VM zugeordnet sind.  Für das Erreichen maximaler Leistung sind entweder der neueste WS2019-Build oder Ubuntu 18.04 oder 16.04 aus dem Azure Marketplace erforderlich.  Die Schreibleistung weicht je nach EA-Größe, Laufwerksauslastung und Kapazitätsnutzung ab.

<sup>4</sup> VMs der Lsv2-Reihe stellen keinen Hostcache für Datenträger bereit, da dieser den Lsv2-Workloads nicht zugute kommt.  Allerdings können Lsv2-VMs die kurzlebige VM-Betriebssystemdatenträger-Option (bis zu 30 GiB) von Azure bieten.

## <a name="size-table-definitions"></a>Definitionen der Größentabelle

- Speicherkapazität wird in GiB-Einheiten oder 1.024^3 Bytes angezeigt. Beachten Sie beim Vergleich von in GB (1.000^3 Bytes) gemessenen Datenträgern mit in GiB (1.024^3) gemessenen Datenträgern, dass die in GiB angegebenen Kapazitätszahlen kleiner erscheinen können. Beispiel: 1.023 GiB = 1.098,4 GB.
- Der Datenträgerdurchsatz wird in E/A-Vorgängen pro Sekunde (Input/Output Operations Per Second, IOPS) und MB/s gemessen, wobei MB/s = 10^6 Bytes/Sekunde beträgt.
- Die beste Leistung für Ihre virtuellen Computer erhalten Sie, wenn Sie die Anzahl von Datenträgern auf zwei Datenträger pro vCPU beschränken.
- **Erwartete Netzwerkbandbreite** ist die maximale aggregierte [Bandbreite pro VM-Typ](../articles/virtual-network/virtual-machine-network-throughput.md), die NIC-übergreifend für alle Ziele zugeordnet ist. Obergrenzen werden zwar nicht garantiert, können aber bei der Wahl des passenden VM-Typs für die geplante Anwendung als Richtwert herangezogen werden. Die tatsächliche Netzwerkleistung hängt von unterschiedlichen Faktoren ab. Hierzu zählen beispielsweise Netzwerküberlastung, Anwendungslasten und die Netzwerkeinstellungen. Informationen zum Optimieren des Netzwerkdurchsatzes finden Sie unter [Optimieren des Netzwerkdurchsatzes für virtuelle Azure-Computer](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Unter Umständen muss eine bestimmte Version gewählt oder der virtuelle Computer optimiert werden, um die erwartete Netzwerkbandbreite unter Linux oder Windows zu erzielen. Weitere Informationen finden Sie unter [Testen der Bandbreite/des Durchsatzes (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).
