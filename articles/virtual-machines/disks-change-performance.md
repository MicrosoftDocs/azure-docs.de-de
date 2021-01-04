---
title: Leistungsstufen für Azure Managed Disks
description: Erfahren Sie mehr über die Leistungsstufen für Managed Disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 28980756ac9e41c9477d687ea9df608b512759e3
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94986781"
---
# <a name="performance-tiers-for-managed-disks"></a>Leistungsstufen für Managed Disks

Die Leistung Ihres verwalteten Azure-Datenträgers wird bei seiner Erstellung über die Leistungsstufe festgelegt. Die Leistungsstufe bestimmt die IOPS und den Durchsatz Ihres verwalteten Datenträgers. Wenn Sie die bereitgestellte Größe Ihres Datenträgers festlegen, wird automatisch eine Leistungsstufe ausgewählt. Sie können die Leistungsstufe bei der Bereitstellung oder danach ändern, ohne die Größe des Datenträgers zu ändern.

Wenn Sie die Leistungsstufe ändern, können Sie sich auf eine höhere Nachfrage vorbereiten und darauf reagieren, ohne Datenträgerbursting zu verwenden. Je nachdem, wie lange die zusätzliche Leistung benötigt wird, kann es kostengünstiger sein, die Leistungsstufe zu ändern, anstatt Bursting zu nutzen. Diese Funktion eignet sich ideal für Ereignisse, die vorübergehend ein konstant höheres Leistungsniveau erfordern, z. B. Feiertagseinkäufe, Leistungstests oder die Ausführung einer Trainingsumgebung. Um diese Ereignisse zu verarbeiten, können Sie eine höhere Leistungsstufe verwenden, solange Sie diese benötigen. Anschließend können Sie zur ursprünglichen Stufe zurückkehren, wenn Sie die zusätzliche Leistung nicht mehr benötigen.

## <a name="how-it-works"></a>Funktionsweise

Wenn Sie einen Datenträger zum ersten Mal bereitstellen, wird die Baselineleistungsstufe für diesen Datenträger auf der Grundlage der bereitgestellten Datenträgergröße festgelegt. Sie können eine höhere Leistungsstufe als die ursprüngliche Baseline verwenden, um höhere Anforderungen zu erfüllen. Wenn Sie diese Leistungsstufe nicht mehr benötigen, können Sie zur anfänglichen Baselineleistungsstufe zurückkehren.

Ihre Abrechnung ändert sich mit geänderter Leistungsstufe. Wenn Sie z. B. einen P10-Datenträger (128 GiB) zur Verfügung stellen, wird Ihre Baselineleistungsstufe auf P10 (500 IOPS und 100 MBit/s) festgelegt. Ihnen wird die P10-Rate in Rechnung gestellt. Sie können die Stufe auf die Leistung von P50 (7.500 IOPS und 250 MBit/s) aktualisieren, ohne die Größe des Datenträgers zu erhöhen. Während des Upgrades wird Ihnen die P50-Rate in Rechnung gestellt. Wenn Sie die höhere Leistung nicht mehr benötigen, können Sie zur P10-Stufe zurückkehren. Der Datenträger wird erneut mit der P10-Rate abgerechnet.

| Datenträgergröße | Baselineleistungsstufe | Kann aktualisiert werden auf |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Keine |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Keine |

Informationen zur Abrechnung finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Beschränkungen

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Ändern der Leistungsstufe finden Sie in den Artikeln zum [Portal](disks-performance-tiers-portal.md) und zu [PowerShell/Befehlszeilenschnittstelle](disks-performance-tiers.md).

