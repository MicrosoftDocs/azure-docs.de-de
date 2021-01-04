---
title: Leistung der VM-Größen der HB-Serie
description: Hier finden Sie Leistungstestergebnisse für VM-Größen der HB-Serie in Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 10d1bfea4527eb7ab9a341de8f12a32cb59597eb
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966971"
---
# <a name="hb-series-virtual-machine-sizes"></a>VM-Größen der HB-Serie

Für die Größen der HB-Serie wurden zahlreiche Leistungstests durchgeführt. Im Anschluss finden Sie einige der Ergebnisse dieser Leistungstests:

| Workload                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 260 GB/s (32–33 GB/s pro CCX)  |
| High-Performance Linpack (HPL)                  | 1\.000 GigaFLOPs (Rpeak), 860 GigaFLOPs (Rmax) |
| RDMA-Wartezeit und -Bandbreite                        | 1,27 Mikrosekunden, 99,1 GBit/s   |
| FIO auf lokaler NVMe-SSD                           | 1,7 GB/s Lesen, 1,0 GB/s Schreiben      |  
| IOR auf vier* Azure SSD Premium-Datenträgern (verwaltete Datenträger vom Typ „P30“, RAID0)**  | 725 MB/s Lesen, 780 MB/s Schreiben   |


## <a name="mpi-latency"></a>MPI-Latenz

Der MPI-Latenztest von der OSU-Microbenchmark-Suite wird ausgeführt. Beispielskripts finden Sie auf [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="MPI-Latenz in Azure HB":::

## <a name="mpi-bandwidth"></a>MPI-Bandbreite

Der MPI-Bandbreitentest von der OSU-Microbenchmark-Suite wird ausgeführt. Beispielskripts finden Sie auf [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="MPI-Bandbreite in Azure HB":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

Das Paket [Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) enthält viele InfiniBand-Tests, z. B. Latenz (ib_send_lat) und Bandbreite (ib_send_bw). Im Folgenden finden Sie einen Beispielbefehl.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute) etwas über die neuesten Ankündigungen, und machen Sie sich mit einigen HPC-Beispielen (High Performance Computing) und -Ergebnissen vertraut.
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).
