---
title: NDv2-Serie
description: Spezifikationen für die VMs der NDv2-Serie
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 97eb134a22bbf2304c39fe7c55457e394467f1d2
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095273"
---
# <a name="updated-ndv2-series"></a>Aktualisierte NDv2-Serie

Die VMs der NDv2-Serie sind ein neues Mitglied der GPU-Familie und darauf ausgelegt, die Anforderungen von besonders ressourcenintensiven Workloads zu erfüllen – beispielsweise KI-Workloads mit GPU-Beschleunigung, Machine Learning-, Simulations- und HPC-Workloads.

Die VMs der NDv2-Serie sind mit 8 NVIDIA Tesla V100-GPUs mit NVLINK-Bus ausgestattet und umfassen jeweils 32 GB an GPU-Arbeitsspeicher. Jede NDv2-VM verfügt außerdem über 40 Intel Xeon Platinum 8168-Kerne (Skylake) ohne Hyperthreading und 672 GiB an Systemarbeitsspeicher.

NDv2-Instanzen bieten dank CUDA GPU-optimierter Computekernel hervorragende Leistung für HPC- und KI-Workloads sowie für zahlreiche KI-, ML- und Analysetools mit integrierter Unterstützung der GPU-Beschleunigung. Dazu zählen beispielsweise TensorFlow, Pytorch, Caffe, RAPIDS und andere Frameworks.

Entscheidend ist, dass die NDv2-Serie sowohl auf rechenintensive Workloads zum zentralen Hochskalieren (mit 8 GPUs pro VM) als auch auf horizontale Skalierung (mit mehreren kombinierten VMs) ausgelegt ist. Die NDv2-Serie unterstützt ab sofort Back-End-Netzwerke mit InfiniBand EDR (100 Gigabit), ähnlich der Datenrate auf HPC-VMs der HB-Serie, und ermöglicht somit Hochleistungsclustering für parallele Szenarien, z. B. für ein verteiltes Training für KI und ML. Dieses Back-End-Netzwerk unterstützt alle wichtigen InfiniBand-Protokolle – einschließlich derer, die in den NCCL2-Bibliotheken von NDVIA verwendet werden. Dadurch ist ein nahtloses Clustering von GPUs möglich.

> [!IMPORTANT]
> Bei [Aktivierung von InfiniBand](./workloads/hpc/enable-infiniband.md) auf der ND40rs_v2-VM verwenden Sie bitte den Mellanox OFED-Treiber 4.7-1.0.0.1.
>
> Aufgrund des größeren GPU-Arbeitsspeichers werden für die neue ND40rs_v2-VM [VMs der 2. Generation](./generation-2.md) und Marketplace-Images benötigt. 
>
> Hinweis: Die ND40s_v2-VM mit 16 GB Arbeitsspeicher pro GPU ist nicht mehr als Vorschauversion verfügbar und wurde durch die aktualisierte ND40rs_v2-VM ersetzt.

<br>

[Storage Premium:](premium-storage-performance.md) Unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen:](generation-2.md) Generation 2<br>
[Beschleunigter Netzwerkbetrieb](../virtual-network/create-vm-accelerated-networking-cli.md): Unterstützt<br>
[Kurzlebige Betriebssystemdatenträger](ephemeral-os-disks.md): Nicht unterstützt <br>
InfiniBand: Unterstützt<br>
Nvidia NVLink Interconnect: Unterstützt<br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximaler Durchsatz des Datenträgers ohne Cache: IOPS/MBps | Max. Netzwerkbandbreite | Maximale Anzahl NICs |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000/800 | 24.000 MBit/s | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Unterstützte Betriebssysteme und Treiber

Um die GPU-Funktionen von virtuellen Azure-Computern der N-Serie nutzen zu können, müssen NVIDIA-GPU-Treiber installiert werden.

Mit der [NVIDIA-GPU-Treibererweiterung](./extensions/hpccompute-gpu-linux.md) werden entsprechende NVIDIA-CUDA- oder GRID-Treiber auf einem virtuellen Computer der N-Serie installiert. Installieren oder verwalten Sie die Erweiterung mithilfe des Azure-Portals oder mit Tools wie Azure PowerShell oder Azure Resource Manager-Vorlagen. Allgemeine Informationen zu VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Azure-Computer](./extensions/overview.md).

Wenn Sie NVIDIA-GPU-Treiber manuell installieren möchten, finden Sie weitere Informationen unter [Installieren von NVIDIA-GPU-Treibern für virtuelle Computer der Serie N mit Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
