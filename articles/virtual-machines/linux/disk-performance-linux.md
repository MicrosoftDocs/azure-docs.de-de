---
title: Leistung von virtuellen Computern und Datenträgern
description: Erfahren Sie mehr darüber, wie VMs und die angefügten Datenträger im Hinblick auf die Leistung zusammenarbeiten.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 092368bb66784a00d5116da0b6be6513f8ebb261
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518061"
---
# <a name="virtual-machine-and-disk-performance"></a>Leistung von virtuellen Computern und Datenträgern
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Nicht im Cache zwischengespeicherte und zwischengespeicherte Grenzwerte virtueller Computer
Virtuelle Computer, für die sowohl Storage Premium als auch die Storage Premium-Zwischenspeicherung aktiviert sind, weisen bei der Speicherbandbreite zwei Einschränkungen auf. Nehmen wir nun den virtuellen Computer Standard_D8s_v3 als Beispiel. Hier ist die Dokumentation zur [Dsv3-Serie](../dv3-dsv3-series.md) und Standard_D8s_v3-VM:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Lassen Sie uns einen Benchmarktest mit dieser Kombination aus virtuellem Computer und Datenträger durchführen, die E/A-Aktivität erzeugt. Weitere Informationen zu Benchmarktests der Speicher-E/A in Azure finden Sie unter [Benchmarktests für Ihre Anwendung in Azure Disk Storage](disks-benchmarks.md). Im Benchmarktool können Sie ersehen, dass die Kombination aus VM und Datenträger 22.800 IOPS erreichen kann:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
