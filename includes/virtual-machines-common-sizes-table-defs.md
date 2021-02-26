---
title: include file
description: include file
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580233"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definitionen der Größentabelle

- Speicherkapazität wird in GiB-Einheiten oder 1.024^3 Bytes angezeigt. Beachten Sie beim Vergleich von in GB (1000^3 Bytes) gemessenen Datenträgern mit in GiB (1024^3) gemessenen Datenträgern, dass die in GiB angegebenen Kapazitätszahlen kleiner erscheinen können. Beispiel: 1.023 GiB = 1.098,4 GB.
- Der Datenträgerdurchsatz wird in E/A-Vorgängen pro Sekunde (Input/Output Operations Per Second, IOPS) und MB/s gemessen, wobei MB/s = 10^6 Bytes/Sekunde beträgt.
- Datenträger können mit oder ohne Cache betrieben werden. Beim Datenträgerbetrieb mit Cache ist der Hostcachemodus auf **ReadOnly** oder **ReadWrite** festgelegt.  Beim Datenträgerbetrieb ohne Cache ist der Hostcachemodus auf **None** festgelegt.
- Weitere Informationen, wie Sie die beste Speicherleistung für Ihre VMs erzielen können, finden Sie unter [Leistung von virtuellen Computern und Datenträgern](../articles/virtual-machines/disks-performance.md).
- **Expected network bandwidth** (Erwartete Netzwerkbandbreite) ist die maximal aggregierte Bandbreite pro VM-Typ, die netzwerkadapterübergreifend für alle Ziele zugeordnet ist. Weitere Informationen finden Sie unter [Netzwerkdurchsatz virtueller Computer](../articles/virtual-network/virtual-machine-network-throughput.md).

  Die Einhaltung von Obergrenzen wird nicht garantiert. Grenzwerte dienen als Richtlinien bei der Auswahl der richtigen VM-Art für die jeweilige Anwendung. Die tatsächliche Netzwerkleistung hängt von mehreren Faktoren ab. Hierzu zählen beispielsweise Netzwerküberlastung, Anwendungslasten und die Netzwerkeinstellungen. Informationen zum Optimieren des Netzwerkdurchsatzes finden Sie unter [Optimieren des Netzwerkdurchsatzes für virtuelle Azure-Computer](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Unter Umständen muss eine bestimmte Version ausgewählt oder der virtuelle Computer optimiert werden, um die erwartete Netzwerkbandbreite unter Linux oder Windows zu erzielen. Weitere Informationen finden Sie unter [Testen der Bandbreite/des Durchsatzes (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



