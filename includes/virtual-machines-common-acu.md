---
title: include file
description: include file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn;davberg
ms.custom: include file
ms.openlocfilehash: ca0c45b5d74140e0398595070df71da669caab53
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178090"
---
Das Konzept der Azure-Compute-Einheit (Azure Compute Unit, ACU) bietet eine Möglichkeit zum Vergleichen der Rechenleistung (CPU) zwischen den Azure-SKUs. Auf diese Weise können Sie leicht feststellen, welche SKU Ihren Leistungsanforderungen am ehesten entspricht.  Zurzeit ist der ACU-Wert auf einem kleinen virtuellen Computer (Standard_A1) auf den Standardwert 100 festgelegt, und an den übrigen SKUs kann ungefähr abgelesen werden, wie viel schneller die jeweilige SKU einen Standard-Benchmarktest ausführen kann. 

> [!IMPORTANT]
> Die ACU ist nur ein Richtwert.  Die Ergebnisse für Ihre Workload können abweichen. 
> 
> 

<br>

| SKU-Familie | ACU\vCPU | vCPU: Core |
| --- | --- |---|
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 | 1:1 |
| [A1–A4](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A5–A7](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A1_v2–A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A2m_v2–A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A8–A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* | 1:1 |
| [D1–D14](../articles/virtual-machines/windows/sizes-general.md) |160–250 | 1:1 |
| [D1_v2–D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* | 1:1 |
| [DS1–DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160–250 | 1:1 |
| [DS1_v2–DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210 - 250* | 1:1 |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2–F72s_v2](../articles/virtual-machines/windows/sizes-compute.md) |195–210* | 2:1\*\*\* |
| [F1–F16](../articles/virtual-machines/windows/sizes-compute.md) |210 - 250* | 1:1 |
| [F1s–F16s](../articles/virtual-machines/windows/sizes-compute.md) |210 - 250* | 1:1 |
| [G1–G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [GS1–GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 - 300* | 1:1 |
| [HB](../articles/virtual-machines/windows/sizes-hpc.md) |199 - 216** | 1:1 |
| [HC](../articles/virtual-machines/windows/sizes-hpc.md) |297 - 315* | 1:1 |
| [L4s–L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* | 1:1 |
| [L8s_v2–L80s_v2](../articles/virtual-machines/windows/sizes-storage.md) |150–175** | 2:1 |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160–180 | 2:1\*\*\* |

*ACUs verwenden die Intel® Turbo-Technologie, um die CPU-Frequenz zu erhöhen und eine Leistungssteigerung zu erzielen.  Das Maß der Leistungssteigerung variiert basierend auf der Größe und Workload des virtuellen Computers sowie auf anderen Workloads, die auf dem gleichen Host ausgeführt werden.

**ACUs verwenden die AMD® Boost-Technologie, um die CPU-Frequenz zu erhöhen und eine Leistungssteigerung zu erzielen.  Das Maß der Leistungssteigerung variiert basierend auf der Größe und Workload des virtuellen Computers sowie auf anderen Workloads, die auf dem gleichen Host ausgeführt werden.

***Mit Hyperthreading und der Möglichkeit zum Ausführen geschachtelter Virtualisierungen
