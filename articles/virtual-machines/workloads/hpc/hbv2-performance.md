---
title: Leistung der VM-Größen der HBv2-Serie
description: Hier finden Sie Leistungstestergebnisse für VM-Größen der HBv2-Serie in Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b782f3d435b7b3737de09b7a12d0fb17aa5f1edc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963316"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>VM-Größen der HBv2-Serie

Für die VM-Größen der [HBv2-Serie](../../hbv2-series.md) wurden zahlreiche Leistungstests durchgeführt. Im Anschluss finden Sie einige der Ergebnisse dieser Leistungstests:


| Workload                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 350 GBit/s (21–23 GBit/s pro CCX)                                     |
| High-Performance Linpack (HPL)                  | 4 TeraFLOPS (Rpeak, FP64), 8 TeraFLOPS (Rmax, FP32)               |
| RDMA-Wartezeit und -Bandbreite                        | 1,2 Mikrosekunden, 190 GBit/s                                        |
| FIO auf lokaler NVMe-SSD                           | 2,7 GBit/s Lesevorgänge, 1,1 GBit/s Schreibvorgänge; 102.000 IOPS-Lesevorgänge, 115.000 IOPS-Schreibvorgänge |
| IOR auf acht* Azure SSD Premium-Datenträgern (verwaltete Datenträger vom Typ „P40“, RAID0)**  | 1,3 GBit/s Lesevorgänge, 2,5 GBit/s Schreibvorgänge; 101.000 IOPS-Lesevorgänge, 105.000 IOPS-Schreibvorgänge |


## <a name="mpi-latency"></a>MPI-Latenz

Der MPI-Latenztest von der OSU-Microbenchmark-Suite wird ausgeführt. Beispielskripts finden Sie auf [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="MPI-Latenz in Azure HB":::


## <a name="mpi-bandwidth"></a>MPI-Bandbreite

Der MPI-Bandbreitentest von der OSU-Microbenchmark-Suite wird ausgeführt. Beispielskripts finden Sie auf [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="MPI-Bandbreite in Azure HB":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

Das Paket [Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) enthält viele InfiniBand-Tests, z. B. Latenz (ib_send_lat) und Bandbreite (ib_send_bw). Im Folgenden finden Sie einen Beispielbefehl. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie in den [Tech Community-Blogs zu Azure Compute](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute) etwas über die neuesten Ankündigungen, und machen Sie sich mit einigen HPC-Beispielen (High Performance Computing) und -Ergebnissen vertraut.
- Eine allgemeinere Übersicht über die Architektur für die Ausführung von HPC-Workloads finden Sie unter [High Performance Computing (HPC) in Azure](/azure/architecture/topics/high-performance-computing/).