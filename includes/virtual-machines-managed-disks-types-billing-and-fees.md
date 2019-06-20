---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 268467796e67caf2d20fedb44d83fd455a09b83e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178078"
---
**Ausgehende Datenübertragungen**: [Ausgehende Datenübertragungen](https://azure.microsoft.com/pricing/details/bandwidth/) (Daten, die von den Azure-Datencentern ausgehen) verursachen Kosten bei der Bandbreitenverwendung.

**Transaktionen:** Ihnen wird die Anzahl von Transaktionen berechnet, die Sie für einen verwalteten Standarddatenträger durchführen. Für SSD Standard gilt jeder E/A-Vorgang kleiner oder gleich 256 KiB Durchsatz als ein einzelner E/A-Vorgang. E/A-Vorgänge mit einem Durchsatz größer als 256 KiB gelten als mehrere Ein-bzw. Ausgabevorgänge der Größe 256 KiB. Für HDD Standard wird jeder E/A-Vorgang als einzelne Transaktion betrachtet, und zwar unabhängig von der E/A-Größe.

Ausführliche Informationen zu den Preisen für Managed Disks, einschließlich der Transaktionskosten, finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Reservierungsgebühr für SSD Ultra-VM

Virtuelle Azure-Computer können angeben, ob sie mit Ultra SSD kompatibel sind. Ein mit Ultra-Datenträgern kompatibler virtueller Computer weist dedizierte Bandbreitenkapazität zwischen der Compute-VM-Instanz und der Blockspeicher-Skalierungseinheit zu, um die Leistung zu optimieren und die Wartezeiten zu verringern. Das Hinzufügen dieser Funktion auf dem virtuellen Computer führt dazu, dass die Reservierungsgebühr nur erhoben wird, wenn Sie die Ultra-Datenträgerfunktion auf dem virtuellen Computer aktiviert haben, ohne ihr einen Ultra-Datenträger anzufügen. Wenn ein Ultra-Datenträger an den mit Ultra-Datenträgern kompatiblen virtuellen Computer angefügt ist, fällt diese Gebühr nicht an. Diese Gebühr fällt pro auf der VM bereitgestellter vCPU an.

Preisdetails für Ultra Disk-Datenträger finden Sie auf der [Seite mit der Preisübersicht für Azure-Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).