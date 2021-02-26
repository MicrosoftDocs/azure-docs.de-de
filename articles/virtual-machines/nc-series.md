---
title: 'NC-Serie: Azure Virtual Machines'
description: Spezifikationen für die VMs der NC-Serie
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 759ae69784e9e9e1c472ffa7f00ae07e28f9b37e
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091696"
---
# <a name="nc-series"></a>NC-Serie

Virtuelle Computer der NC-Serie werden mit der [NVIDIA-Grafikkarte Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) und dem Prozessor Intel Xeon E5-2690 v3 (Haswell) betrieben. Benutzer können Daten schneller analysieren, indem sie CUDA für Anwendungen zur Energieuntersuchung, Absturzsimulationen, Rendering mit Raytracing, Deep Learning und mehr verwenden. Die NC24r-Konfiguration bietet eine Netzwerkschnittstelle mit geringer Wartezeit und hohem Durchsatz, die sich ideal für die Verarbeitung eng gekoppelter paralleler Computingworkloads eignet.

[Storage Premium:](premium-storage-performance.md) Nicht unterstützt<br>
[Storage Premium-Zwischenspeicherung:](premium-storage-performance.md) Nicht unterstützt<br>
[Livemigration:](maintenance-and-updates.md) Nicht unterstützt<br>
[Updates mit Speicherbeibehaltung:](maintenance-and-updates.md) Nicht unterstützt<br>
[Unterstützung von VM-Generationen](generation-2.md): Generation 1<br>
[Beschleunigter Netzwerkbetrieb:](../virtual-network/create-vm-accelerated-networking-cli.md) Nicht unterstützt<br>
[Kurzlebige Betriebssystemdatenträger:](ephemeral-os-disks.md) Nicht unterstützt <br>
Nvidia NVLink Interconnect: Nicht unterstützt<br>
<br>

| Size | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | GPU | GPU-Arbeitsspeicher: GiB | Max. Anzahl Datenträger | Maximale Anzahl NICs |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = halbe K80-Karte

*RDMA-fähig

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Unterstützte Betriebssysteme und Treiber

Um die GPU-Funktionen von virtuellen Azure-Computern der N-Serie nutzen zu können, müssen NVIDIA-GPU-Treiber installiert werden.

Mit der [NVIDIA-GPU-Treibererweiterung](./extensions/hpccompute-gpu-windows.md) werden entsprechende NVIDIA-CUDA- oder GRID-Treiber auf einem virtuellen Computer der N-Serie installiert. Installieren oder verwalten Sie die Erweiterung mithilfe des Azure-Portals oder mit Tools wie Azure PowerShell oder Azure Resource Manager-Vorlagen. Informationen zu unterstützten Betriebssystemen und Bereitstellungsschritten finden Sie in der [Dokumentation zur NVIDIA-GPU-Treibererweiterung](./extensions/hpccompute-gpu-windows.md). Allgemeine Informationen zu VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Azure-Computer](./extensions/overview.md).

Wenn Sie NVIDIA-GPU-Treiber manuell installieren möchten, finden Sie Informationen zu unterstützten Betriebssystemen und Treibern sowie Schritte zur Installation und zur Überprüfung unter [Einrichten von GPU-Treibern der N-Serie für Windows](./windows/n-series-driver-setup.md) bzw. [Einrichten von GPU-Treibern der N-Serie für Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Andere Größen

- [Allgemeiner Zweck](sizes-general.md)
- [Arbeitsspeicheroptimiert](sizes-memory.md)
- [Speicheroptimiert](sizes-storage.md)
- [GPU-optimiert](sizes-gpu.md)
- [High Performance Computing](sizes-hpc.md)
- [Vorherige Generationen](sizes-previous-gen.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mit [Azure-Computeeinheiten (ACU)](acu.md) die Computeleistung von Azure-SKUs vergleichen können.
